

## Learning Objectives

By the end of this chapter, you will understand:

- What Plan-and-Execute is
- How it differs from ReAct
- When to use it
- Why it is popular in Agentic AI
- Why real systems usually allow re-planning, not just a single rigid plan
- How Plan-and-Execute and ReAct are often combined, not chosen exclusively
- Why Plan-and-Execute is usually cheaper than pure ReAct
- How LangGraph's actual Plan-and-Execute pattern is structured

---

# Introduction

Imagine your manager says:

```text
Build an Employee Management System.
```

Would you immediately start coding?

No.

You would first create a plan.

Example

```text
Design Database

↓

Build Backend

↓

Create APIs

↓

Build Frontend

↓

Testing

↓

Deployment
```

After the plan is ready,

you start executing each step.

AI Agents can work the same way.

This approach is called **Plan-and-Execute**.

---

# What is Plan-and-Execute?

Plan-and-Execute is a reasoning pattern where the AI first creates a complete plan,

then executes each step one by one.

Unlike ReAct,

it doesn't decide the next action after every step.

It decides everything upfront.

---

# Visual Flow

```text
Goal

↓

Create Plan

↓

Execute Step 1

↓

Execute Step 2

↓

Execute Step 3

↓

Final Answer
```

---

# Example

User

```text
Plan a 5-day trip to Japan.
```

The AI first creates a plan.

```text
Step 1

Choose Cities

↓

Step 2

Estimate Budget

↓

Step 3

Suggest Hotels

↓

Step 4

Plan Daily Activities

↓

Step 5

Generate Final Itinerary
```

Only after creating the plan,

it starts executing each step.

---

# Why Does It Work?

Large tasks are easier to manage when planned first.

Planning helps the AI:

- Stay organized
- Avoid missing steps
- Produce more structured results

---

# In Practice, the Plan Usually Isn't Fully Rigid

The description so far makes it sound like the plan is fixed forever once created. In real implementations, this is usually softened with **re-planning**.

```text
Create Plan

↓

Execute Step 1 → Success → Continue

↓

Execute Step 2 → FAILS or produces unexpected result

↓

Re-planner reviews progress + failure

↓

Updated Plan (remaining steps revised)

↓

Continue Execution
```

This matters because a plan made _before_ execution can be based on wrong assumptions — a step might fail, an API might return something unexpected, or new information might make later steps unnecessary or insufficient. A pure "plan once, never revisit" approach is brittle in practice. Most production Plan-and-Execute systems include a re-planning step specifically to handle this, without falling all the way back to ReAct's step-by-step reasoning for the entire task.

---

# ReAct vs Plan-and-Execute

|ReAct|Plan-and-Execute|
|---|---|
|Think → Act → Think Again|Plan Once → Execute|
|Decides one step at a time|Decides the entire workflow first|
|Flexible|Structured|
|Better for changing situations|Better for predictable tasks|

---

# Why Plan-and-Execute Is Often Cheaper Than Pure ReAct

Worth knowing as a practical, interview-relevant comparison.

In ReAct, the model re-reasons after every single action — meaning a full "Thought" generation happens before each tool call, for every step. In Plan-and-Execute, the expensive reasoning (deciding the whole sequence of steps) happens **once**, and execution of each step can often be simpler or even handled by smaller/cheaper models or non-LLM code.

```text
ReAct: N steps → roughly N reasoning calls (one before each action)
Plan-and-Execute: N steps → 1 planning call + N (often lighter) execution calls
```

This is one practical reason Plan-and-Execute is attractive for cost-sensitive, high-volume production workflows where the task shape is fairly predictable.

---

# The Common Real-World Pattern: Combining Both

This is worth stating directly, since the earlier framing of "choose ReAct or choose Plan-and-Execute" oversimplifies how these are actually used together.

```text
High level:  Plan-and-Execute
             → break the goal into a sequence of major steps

Within each step:  ReAct
             → reason, act, observe, possibly loop, to actually
               complete that specific step
```

Example: planning a 5-day Japan trip might use Plan-and-Execute to lay out "Choose Cities → Estimate Budget → Suggest Hotels → ...", but the "Suggest Hotels" step itself might use a ReAct loop internally (search hotels → observe prices → search again if over budget → finalize). This hybrid is extremely common in real agent architectures — it's rarely a strict either/or choice.

---

# Real-World Example

Suppose you're building an AI coding assistant.

User

```text
Create a Hospital Management System.
```

Instead of immediately generating code,

the AI plans.

```text
Database Design

↓

Authentication

↓

Patient Module

↓

Doctor Module

↓

Appointment Module

↓

Testing
```

Then it starts generating code for each module.

---

# When Should You Use It?

Use Plan-and-Execute for:

- Software projects
- Travel planning
- Research reports
- Business plans
- Large document generation

Any task that can be planned before execution.

---

# When Should You Avoid It?

Avoid it when the environment changes continuously.

Example

```text
Find the cheapest flight right now.
```

Flight prices change frequently.

The AI may need to:

- Search
- Observe
- Search again

ReAct is a better choice here.

---

# ReAct or Plan-and-Execute?

Think about driving.

ReAct

```text
Drive

↓

Traffic Changes

↓

Choose Another Route

↓

Continue Driving
```

The plan changes as new information arrives.

---

Plan-and-Execute

```text
Create Full Route

↓

Follow Route

↓

Reach Destination
```

The route is decided before driving.

(In practice, even this analogy usually includes re-planning — think of it as following a GPS route that recalculates if you miss a turn or hit unexpected traffic, rather than a route that's rigidly followed no matter what happens.)

---

# Why is Plan-and-Execute Important?

Many modern AI Agents first create a plan,

then execute it.

This produces:

- Better organization
- More predictable workflows
- Easier debugging

Frameworks like LangGraph often implement workflows using this pattern.

---

# How LangGraph Structures This, Concretely

Since LangGraph has come up throughout this book, here's the typical node structure used for Plan-and-Execute style agents:

```text
[Planner Node]    → produces an ordered list of steps
      ↓
[Executor Node]    → executes the current step (may itself run a ReAct loop)
      ↓
[Replanner Node]   → checks progress; decides whether to continue with the
                     existing plan, revise it, or finish
      ↓
(loops back to Executor, or exits with final answer)
```

This mirrors the re-planning concept described earlier, but implemented as explicit graph nodes and edges rather than something the model has to manage entirely through generated text — the same theme seen when LangGraph was discussed in the ReAct chapter.

---

# Best Practice

Use Plan-and-Execute when:

- The goal is large
- The steps are predictable
- Planning improves execution

Don't use it for simple questions.

---

# Common Beginner Mistakes

### Mistake 1

Thinking Plan-and-Execute replaces ReAct.

It doesn't.

Both solve different problems, and are frequently combined.

---

### Mistake 2

Creating an overly detailed plan.

The plan should guide execution,

not become a project by itself.

---

### Mistake 3

Using Plan-and-Execute for dynamic tasks.

If the environment changes frequently,

ReAct is usually the better choice.

---

### Mistake 4

Treating the initial plan as permanently fixed, with no mechanism to revise it if a step fails or new information changes what's needed.

---

# Interview Tip ⭐

A common interview question is:

> **When would you choose ReAct over Plan-and-Execute?**

A good answer is:

- Use **ReAct** when the AI must continuously observe and react to new information.
- Use **Plan-and-Execute** when the task can be fully planned before execution.

---

# Interview Tip ⭐

A stronger follow-up worth preparing for:

> **Would you ever combine ReAct and Plan-and-Execute in the same system?**

Answer:

Yes — this is actually the common pattern in production agents. Plan-and-Execute is used at the high level to break a goal into major steps, while ReAct is often used within each individual step to handle the actual reasoning-and-tool-use needed to complete it. Additionally, most real Plan-and-Execute systems include a re-planning step so the plan can adapt if a step fails or the situation changes, rather than rigidly following the original plan no matter what.

---

# Key Takeaways

- Plan-and-Execute separates planning from execution.
- The AI creates a complete plan before acting.
- Real systems usually add re-planning, so the plan can adapt when a step fails or circumstances change.
- Plan-and-Execute is often cheaper than pure ReAct, since the expensive reasoning happens once (the plan) rather than before every single action.
- It works well for structured, predictable tasks.
- ReAct is dynamic; Plan-and-Execute is structured — but in practice they're frequently combined, with ReAct handling execution within individual planned steps.
- LangGraph implements this pattern explicitly with planner, executor, and replanner nodes.
- Both are widely used in modern Agentic AI systems.

---

