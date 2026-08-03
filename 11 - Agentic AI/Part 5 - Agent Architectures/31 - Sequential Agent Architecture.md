

## Learning Objectives

By the end of this chapter, you will understand:

- What a Sequential Agent Architecture is
- Why Agents execute sequentially
- How a Sequential Agent workflow operates
- Variations on the basic sequential pattern
- How to validate, checkpoint, and recover from failures mid-pipeline
- Advantages and limitations of Sequential Architectures
- When to use a Sequential Agent Architecture

---

# Introduction

Imagine you ask an AI system:

```text
Create a technical report about Artificial Intelligence.
```

The work is divided among multiple Agents.

However,

each Agent must wait for the previous Agent to finish.

Example

```text
Research Agent

↓

Writer Agent

↓

Reviewer Agent

↓

Publisher Agent
```

Each Agent depends on the output of the previous Agent.

This is called a **Sequential Agent Architecture**.

---

# What is a Sequential Agent Architecture?

A Sequential Agent Architecture is a Multi-Agent system where Agents execute **one after another** in a predefined order.

Each Agent receives the output of the previous Agent,

processes it,

and passes the result to the next Agent.

---

# Visual Diagram

```text
User

↓

Research Agent

↓

Writer Agent

↓

Reviewer Agent

↓

Publisher Agent

↓

Final Response
```

The workflow follows a fixed sequence.

---

# Why Use Sequential Architectures?

Some tasks have dependencies.

An Agent cannot begin until another Agent has finished.

Example

```text
Collect Requirements

↓

Design System

↓

Write Code

↓

Test Application
```

Testing cannot begin before the code exists.

---

# How Does It Work?

The workflow follows these steps.

```text
User Request

↓

Agent 1

↓

Output

↓

Agent 2

↓

Output

↓

Agent 3

↓

Final Result
```

Each Agent builds on the previous Agent's work.

---

# Data Flow

In a Sequential Architecture,

information flows in one direction.

```text
Agent A

↓

Agent B

↓

Agent C

↓

Agent D
```

Earlier Agents influence later Agents,

but later Agents usually do not modify earlier work.

---

# Example

User

```text
Write a business proposal.
```

Workflow

```text
Research Agent

↓

Gather Information

↓

Writer Agent

↓

Create Proposal

↓

Reviewer Agent

↓

Improve Proposal

↓

Final Response
```

Each Agent has a clearly defined responsibility.

---

# Python Example

A simplified workflow:

```python
research = ResearchAgent()

writer = WriterAgent()

reviewer = ReviewerAgent()

research_data = research.run()

draft = writer.run(research_data)

final = reviewer.run(draft)

print(final)
```

Each Agent waits until the previous Agent finishes.

---

# Variations on the Sequential Pattern

Not every sequential pipeline is a rigid, one-track chain. A few common variations:

### Strict Sequential

The order never changes. Every run passes through every Agent, in the same order, every time. This matches the basic examples above.

### Sequential with Validation Gates

```text
Agent A → Validate → Agent B → Validate → Agent C
```

A lightweight check (schema check, keyword check, or a small verifier Agent) runs between stages. If validation fails, the pipeline retries the current stage instead of passing bad data forward.

### Sequential with Conditional Branching

```text
Agent A → Decision Point ──▶ Agent B (if condition X)
                        └──▶ Agent C (if condition Y)
```

The overall flow is still one-step-at-a-time, but which Agent runs next depends on the previous output. This is sometimes called a "sequential-with-routing" pattern and is common in frameworks like LangGraph.

---

# Validation Gates Between Agents

Because later Agents trust earlier output, a single bad result can quietly corrupt everything downstream. A validation gate is a small check placed between two Agents.

```python
def run_with_validation(agent, input_data, validator):
    output = agent.run(input_data)
    if not validator(output):
        # retry once with feedback about what failed
        output = agent.run(input_data, feedback="Output failed validation, please retry")
    return output
```

Validators can be as simple as a schema check (does the output have the required fields?) or as involved as a dedicated Critic Agent that scores the output before it's allowed to proceed.

---

# Checkpointing and Recovery

Long sequential pipelines are expensive to restart from scratch if a later stage fails. Saving the output of each Agent as a checkpoint lets the pipeline resume from the last successful stage rather than re-running everything.

```python
def run_pipeline(task, checkpoints={}):
    stages = [("research", ResearchAgent()), ("writer", WriterAgent()), ("reviewer", ReviewerAgent())]
    result = task

    for name, agent in stages:
        if name in checkpoints:
            result = checkpoints[name]  # skip stages already completed
            continue
        result = agent.run(result)
        checkpoints[name] = result  # persist to disk/db in a real system

    return result
```

This is especially useful for pipelines that take minutes rather than seconds, or that involve costly steps like large document generation.

---

# Advantages

### Simple Workflow

The execution order is easy to understand.

---

### Predictable Results

Every task follows the same sequence.

---

### Easier Debugging

If something goes wrong,

it's easier to identify which Agent caused the problem.

---

### Suitable for Dependent Tasks

Sequential Architectures work well when every step depends on previous work.

---

### Easy to Add Checkpoints

Because each stage has a clear boundary, it's straightforward to save progress and resume after a failure.

---

# Limitations

### Slower Execution

Each Agent must wait for the previous Agent.

---

### No Parallel Processing

Independent tasks cannot execute simultaneously.

---

### Bottlenecks

A slow Agent delays every Agent that follows.

---

### Single Failure Stops the Pipeline

If one Agent fails,

later Agents cannot continue.

---

### Compounding Errors

Because each Agent trusts the previous Agent's output by default, an early mistake can flow through every remaining stage unless validation gates are in place.

---

# Sequential vs Parallel Execution

|Sequential|Parallel|
|---|---|
|One Agent at a time|Multiple Agents simultaneously|
|Dependent tasks|Independent tasks|
|Easier to coordinate|Faster execution|
|Higher latency|Lower latency|
|Errors are easier to trace to one stage|Errors are harder to trace across concurrent Agents|

Choose the architecture based on task dependencies.

---

# Real-World Example

Imagine an AI Software Development Pipeline.

```text
Requirement Agent

↓

Architecture Agent

↓

Backend Agent

↓

Testing Agent

↓

Documentation Agent
```

Each stage depends on the previous one,

making a Sequential Architecture the natural choice.

---

# Industry Insight

Many enterprise workflows are naturally sequential.

Examples include:

- Software development pipelines
- Legal document review
- Financial approval processes
- Medical report generation

Frameworks like LangGraph and CrewAI often implement sequential workflows as directed execution graphs. LangGraph in particular represents a sequential pipeline as a graph of nodes and edges, which makes it straightforward to add a conditional branch or a validation gate later without rewriting the whole pipeline.

---

# Performance Considerations

Since sequential pipelines add up the latency of every stage, a few techniques help keep them fast:

- **Stream partial output** where possible, so the user sees progress (e.g., a draft appearing) instead of waiting for the entire pipeline to finish.
- **Cache repeatable steps** — if the Research Agent's output won't change for a given input, cache it instead of re-running it on retries.
- **Set a per-stage timeout** so one slow Agent doesn't stall the whole pipeline indefinitely.
- **Measure end-to-end latency, not just per-agent latency** — the sum of individually "fast" stages can still be a slow overall pipeline.

---

# Best Practices

Use Sequential Architectures when tasks depend on previous outputs.

Keep each Agent focused on one responsibility.

Validate each Agent's output before passing it to the next Agent.

Monitor the pipeline for bottlenecks.

Add checkpoints so long pipelines can resume after a failure instead of restarting from scratch.

Set a timeout per stage to prevent one slow Agent from stalling the whole pipeline.

Log the input and output of every stage for easier debugging.

---

# Common Beginner Mistakes

### Mistake 1

Using a Sequential Architecture for independent tasks.

Independent work should often run in parallel.

---

### Mistake 2

Making one Agent responsible for multiple stages.

Each Agent should have a single responsibility.

---

### Mistake 3

Skipping validation between Agents.

One incorrect output can affect the entire pipeline.

---

### Mistake 4

Creating unnecessarily long pipelines.

Too many stages increase latency and complexity.

---

### Mistake 5

Not checkpointing long-running pipelines, forcing a full restart from the beginning after a failure in a late stage.

---

# Interview Tip

A common interview question is:

> **When should you use a Sequential Agent Architecture?**

A good answer is:

A Sequential Agent Architecture should be used when tasks have dependencies and must be completed in a specific order. Each Agent receives the previous Agent's output, processes it, and passes the result to the next Agent.

A strong follow-up point: mention that real sequential pipelines usually add validation gates between stages and checkpointing, so a bad output or a mid-pipeline failure doesn't force a full restart or silently corrupt the final result.

---

# Where is this Used?

- Software Development Pipelines
- Research Report Generation
- Legal Document Processing
- Financial Approval Systems
- Enterprise Workflow Automation

---

# Key Takeaways

- Sequential Agent Architectures execute Agents one after another.
- Each Agent depends on the previous Agent's output.
- They are simple, predictable, and easy to debug.
- Validation gates and checkpoints protect the pipeline from compounding errors and costly restarts.
- They are ideal for workflows with dependent tasks.
- They are less suitable when tasks can be executed independently.

---

