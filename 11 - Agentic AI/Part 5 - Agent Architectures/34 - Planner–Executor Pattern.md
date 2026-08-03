

## Learning Objectives

By the end of this chapter, you will understand:

- What the Planner–Executor Pattern is
- Why planning and execution are separated
- How the Planner and Executor collaborate
- How plans are represented, validated, and executed by one or more Executors
- Advantages and limitations of this architecture
- When to use the Planner–Executor Pattern

---

# Introduction

Imagine you ask an AI system:

```text
Build a REST API for an Employee Management System.
```

Should one Agent:

- Plan the solution
- Write the code
- Test the code
- Fix errors

all at once?

It could.

But a better approach is to separate responsibilities.

One Agent creates the plan.

Another Agent executes it.

This is called the **Planner–Executor Pattern**.

---

# What is the Planner–Executor Pattern?

The Planner–Executor Pattern is an Agent Architecture where one Agent is responsible for planning the work,

while another Agent is responsible for executing the plan.

Instead of mixing reasoning and execution,

each Agent focuses on a single responsibility.

---

# Visual Diagram

```text
                User
                  │
                  ▼
          Planner Agent
                  │
          Execution Plan
                  │
                  ▼
         Executor Agent
                  │
         Perform Tasks
                  │
                  ▼
            Final Response
```

The Planner decides **what** should be done.

The Executor decides **how** to perform each step.

---

# Why Separate Planning and Execution?

Planning and execution require different skills.

The Planner focuses on:

- Understanding the goal
- Breaking the task into steps
- Choosing the execution strategy

The Executor focuses on:

- Calling tools
- Running commands
- Collecting results
- Completing the work

Separating these responsibilities improves clarity and flexibility.

---

# Responsibilities of the Planner

The Planner Agent typically performs:

- Goal analysis
- Task decomposition
- Dependency analysis
- Execution planning
- Replanning when necessary

Example

```text
Build REST API

↓

Design Database

↓

Create Backend

↓

Write Tests

↓

Deploy
```

The Planner creates this roadmap.

---

# How Plans Are Represented

A plan needs a concrete format the Executor can actually consume. Two common approaches:

### Free-Text Plan

```text
1. Design the database schema
2. Create the entity classes
3. Build the REST endpoints
4. Write and run tests
```

Easy for a human to read, but the Executor has to re-interpret natural language on every step, which is less reliable.

### Structured Plan (JSON / Task Graph)

```json
{
  "steps": [
    {"id": 1, "action": "design_schema", "depends_on": []},
    {"id": 2, "action": "create_entities", "depends_on": [1]},
    {"id": 3, "action": "build_endpoints", "depends_on": [2]},
    {"id": 4, "action": "run_tests", "depends_on": [3]}
  ]
}
```

A structured plan lets the Executor (or an orchestrator) know exactly what to run next, what depends on what, and makes it possible to run independent steps in parallel. Most production systems prefer structured plans for anything beyond a simple linear task list.

---

# Responsibilities of the Executor

The Executor Agent performs the actual work.

Example

```text
Create Database

↓

Write Backend Code

↓

Run Tests

↓

Deploy Application
```

The Executor follows the plan provided by the Planner.

---

# Plan Granularity

How detailed should each step be?

- **Too coarse** ("build the backend") forces the Executor to make significant decisions on its own, which defeats the purpose of separating planning from execution.
- **Too fine** ("open file, add line 1, add line 2, ...") makes the plan brittle and forces constant replanning for tiny deviations.

A good rule of thumb: each step should correspond to one clear, independently verifiable unit of work — small enough to check for success or failure, large enough that the Executor still has room to decide _how_ to do it.

---

# Request Flow

A typical Planner–Executor workflow looks like this.

```text
User Request

↓

Planner

↓

Execution Plan

↓

Executor

↓

Tool Calls

↓

Results

↓

Final Response
```

The Planner creates the strategy.

The Executor performs the actions.

---

# Plan Validation

Before execution begins, a plan can be checked for basic soundness:

- Do all referenced tools/resources actually exist and are accessible to the Executor?
- Are there circular dependencies between steps?
- Does the plan actually address every part of the user's original request?

For high-stakes actions (deploying to production, sending external communications, spending money), it's common to add a **human approval step** between planning and execution — the Planner produces the plan, a person reviews it, and only then does the Executor begin.

---

# Replanning

Execution does not always go as expected.

Example

```text
Step 1

Success

↓

Step 2

Failed

↓

Planner

↓

Create New Plan

↓

Executor

↓

Continue
```

The Planner may generate a new plan based on updated information.

This makes the architecture more adaptable.

### Transient vs Permanent Failures

Not every failure needs a full replan. It helps to classify the failure first:

- **Transient failure** (network blip, rate limit, temporary tool error) → simply retry the same step.
- **Permanent failure** (the approach itself is wrong, a required resource doesn't exist) → send it back to the Planner for a new strategy.

Treating every failure as a signal to replan wastes Planner calls; treating every failure as "just retry" causes the Executor to loop pointlessly on unfixable steps.

---

# Interleaved Planning and Execution (ReAct-style)

The examples so far show planning happening entirely upfront, then execution following. An alternative is to interleave them one step at a time — plan a single step, execute it, observe the result, then plan the next step:

```text
Think → Act → Observe → Think → Act → Observe → ...
```

This is the idea behind the **ReAct** pattern popularized in agent research. It trades some upfront predictability for much better adaptability, since the Planner always has the latest real-world result before deciding the next step. Upfront planning tends to suit well-understood tasks; interleaved planning tends to suit exploratory or uncertain ones.

---

# Example

User

```text
Create a data analysis dashboard.
```

Planner

```text
1. Load Dataset

2. Clean Data

3. Build Charts

4. Generate Dashboard
```

Executor

```text
Run Python

↓

Process Data

↓

Generate Charts

↓

Create Dashboard
```

The Planner never writes code.

The Executor never designs the workflow.

---

# Python Example

A simplified example:

```python
planner = Planner()

executor = Executor()

plan = planner.create_plan(
    "Analyze sales data"
)

result = executor.execute(plan)

print(result)
```

Production systems often include multiple Executors,

tool orchestration,

and replanning,

but the architecture remains the same.

---

# Multiple Specialized Executors

A single generic Executor isn't always the best fit. Many production systems route each step of the plan to the Executor best suited for it:

```python
executors = {
    "database": DatabaseExecutor(),
    "code": CodeExecutor(),
    "test": TestExecutor(),
}

for step in plan.steps:
    executor = executors[step.type]
    result = executor.run(step)
```

This mirrors the specialization principle from the Multi-Agent Architecture chapter: the Planner still owns the overall strategy, but different kinds of steps go to Executors built (and permissioned) specifically for that kind of work.

---

# Planner vs Executor

|Planner|Executor|
|---|---|
|Understands the goal|Performs the work|
|Creates the strategy|Executes the strategy|
|Breaks tasks into steps|Uses tools and APIs|
|Can replan|Reports execution results|
|Typically one instance|Can be multiple, specialized instances|

They have different responsibilities but work together.

---

# Advantages

### Clear Separation of Responsibilities

Planning and execution remain independent.

---

### Better Decision Making

The Planner can focus entirely on reasoning.

---

### Easier Maintenance

Planning logic and execution logic can evolve independently.

---

### Dynamic Replanning

If execution fails,

the Planner can generate a new strategy.

---

### Better Scalability

Multiple Executors can execute different parts of a plan.

---

# Limitations

### Additional Coordination

The Planner and Executor must communicate effectively.

---

### Higher Latency

Planning adds an extra step before execution.

---

### More Components

The architecture is more complex than a Single-Agent system.

---

### Poor Plans Affect Execution

If the Planner creates a weak plan,

the Executor cannot produce good results.

---

### Plan Rigidity

An upfront plan that's too detailed can become invalid the moment reality diverges even slightly from what the Planner assumed, forcing frequent replanning.

---

# Real-World Example

Imagine an AI Coding Assistant.

The user asks:

```text
Build a Spring Boot CRUD API.
```

Planner

```text
Design Database

↓

Create Entities

↓

Build REST APIs

↓

Write Tests
```

Executor

```text
Generate Code

↓

Run Build

↓

Execute Tests

↓

Return Project
```

Each Agent performs a specialized role.

---

# Industry Insight

The Planner–Executor Pattern is widely used in modern Agent frameworks.

Examples include:

- LangGraph planning workflows
- OpenAI reasoning-based Agents
- CrewAI task delegation
- Google ADK execution pipelines
- The ReAct pattern (Reason + Act), which interleaves lightweight planning with execution one step at a time

Many enterprise AI systems separate planning from execution because it improves modularity and allows replanning when conditions change.

---

# Best Practices

Keep planning separate from execution.

Validate plans before execution begins.

Allow the Planner to update the plan when execution fails.

Keep Executors focused on performing actions rather than making strategic decisions.

Use a structured plan format (e.g., JSON with dependencies) once the workflow is more than a simple linear list.

Classify failures as transient or permanent before deciding whether to retry a step or send it back to the Planner.

Add a human approval checkpoint between planning and execution for high-stakes or irreversible actions.

---

# Common Beginner Mistakes

### Mistake 1

Making the Executor redesign the plan.

Planning should remain the Planner's responsibility.

---

### Mistake 2

Creating overly detailed plans.

Plans should provide enough guidance without becoming unnecessarily rigid.

---

### Mistake 3

Never replanning.

Real-world environments change.

The Planner should adapt when execution encounters unexpected situations.

---

### Mistake 4

Treating the Planner and Executor as completely independent.

The Executor should continuously report progress so the Planner can adjust if necessary.

---

### Mistake 5

Replanning from scratch on every minor, transient failure instead of simply retrying the affected step.

---

# Interview Tip

A common interview question is:

> **What is the Planner–Executor Pattern?**

A good answer is:

The Planner–Executor Pattern separates reasoning from execution. The Planner analyzes the user's goal and creates an execution plan, while the Executor carries out that plan using tools, APIs, and other resources. This separation improves modularity, maintainability, and adaptability.

A strong follow-up point: mention the tradeoff between upfront planning (more predictable, better for well-understood tasks) and interleaved planning like ReAct (more adaptive, better for exploratory or uncertain tasks), and that plan granularity and failure classification (transient vs permanent) determine how well the system handles the unexpected.

---

# Where is this Used?

- AI Coding Assistants
- Research Agents
- Enterprise Workflow Automation
- Software Development Platforms
- Autonomous AI Systems

---

# Key Takeaways

- The Planner–Executor Pattern separates planning from execution.
- The Planner creates the strategy.
- The Executor performs the work.
- Plans can be represented as free text or, more reliably, as structured task graphs with dependencies.
- Replanning allows the system to adapt when execution changes; classifying failures as transient or permanent avoids unnecessary replanning.
- Planning can happen entirely upfront or be interleaved with execution (ReAct-style), depending on how predictable the task is.
- This architecture is widely used in production AI systems because it improves flexibility and maintainability.

---

