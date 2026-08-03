
## Learning Objectives

By the end of this chapter, you will understand:

- What Goal Analysis is
- Why AI Agents analyze goals before acting
- How goals are broken into sub-goals
- Why Goal Analysis improves planning
- How LLMs actually perform goal analysis in practice
- What makes a sub-goal "good" vs "poor"
- Common challenges when decomposing goals

---

# Introduction

Imagine you ask an AI Agent:

```text
Help me start a healthy lifestyle.
```

Can the Agent immediately create a plan?

Not yet.

The request is too broad.

The Agent first needs to understand:

- What does "healthy lifestyle" mean?
- Is the user asking about exercise?
- Diet?
- Sleep?
- Weight loss?

Only after understanding the goal can it create a good plan.

This process is called **Goal Analysis**.

---

# What is Goal Analysis?

Goal Analysis is the process of understanding **what the user is trying to achieve** before creating a plan.

The Agent identifies:

- The main goal
- The user's intent
- Any missing information
- Possible sub-goals

Only then does it start planning.

---

# Key Components of Goal Analysis

Every goal, no matter how it's phrased, can be broken into four components the Agent needs to extract:

|Component|Description|Example ("Plan my vacation")|
|---|---|---|
|**Intent**|The underlying purpose behind the request|Relaxation / travel experience|
|**Constraints**|Limits the plan must respect|Budget, dates, number of travelers|
|**Context**|Background information already known or inferable|Past preferences, location, season|
|**Success Criteria**|How the Agent knows the goal is achieved|Trip is booked and itinerary confirmed|

If any of these components is missing or ambiguous, the Agent should treat it as **missing information** and either ask a clarifying question or make a clearly stated assumption.

---

# Why is Goal Analysis Important?

If the Agent misunderstands the goal,

it may solve the wrong problem.

Good planning always starts with understanding the objective.

---

# Visual Diagram

```text
User Goal

↓

Analyze Goal

↓

Identify Intent

↓

Break into Sub-goals

↓

Create Plan
```

Goal Analysis happens **before** planning.

---

# Example

User Goal

```text
Plan my vacation.
```

The Agent first asks:

```text
Where?

When?

What's your budget?

How many days?
```

After understanding the goal,

it creates sub-goals.

```text
Book Flights

↓

Find Hotel

↓

Plan Activities

↓

Estimate Budget
```

---

# Another Example

User Goal

```text
Help me prepare for a Java interview.
```

The Agent analyzes the goal.

Possible sub-goals:

```text
Study Core Java

↓

Practice Coding

↓

Learn Spring Boot

↓

Solve Interview Questions
```

Now the Agent understands exactly what needs to be done.

---

# Goal vs Task

Many beginners confuse these two concepts.

|Goal|Task|
|---|---|
|The final objective|A step toward the objective|
|High-level|Specific action|
|"Plan a vacation"|"Book a flight"|

One goal can contain many tasks.

---

# Python Example 🐍

A simplified example:

```python
goal = "Plan a vacation"

sub_goals = [
    "Book flight",
    "Book hotel",
    "Create itinerary"
]

for goal in sub_goals:
    print(goal)
```

Real AI Agents generate these sub-goals automatically using an LLM.

---

# How LLMs Perform Goal Analysis in Practice

In production systems, Goal Analysis is usually done with a dedicated prompt step **before** the planning step. The Agent sends the user's goal to the LLM with instructions to extract structure, not to solve the goal yet.

A simplified prompt template looks like this:

```text
You are a Goal Analysis module for an AI Agent.

Given the user's request, extract the following as JSON:
- main_goal
- intent
- known_constraints
- missing_information (list any questions needed)
- suggested_sub_goals

User request: "{user_input}"
```

Example output for `"Help me prepare for a Java interview"`:

```json
{
  "main_goal": "Prepare for a Java interview",
  "intent": "Get hired / pass technical screening",
  "known_constraints": [],
  "missing_information": ["Experience level?", "Interview date?", "Company/role type?"],
  "suggested_sub_goals": [
    "Study Core Java",
    "Practice Coding",
    "Learn Spring Boot",
    "Solve Interview Questions"
  ]
}
```

This structured output is then passed to the **Planning** module, which turns sub-goals into an ordered, executable plan.

---

# Characteristics of a Good Sub-goal

Not every decomposition is equally useful. Well-formed sub-goals are generally:

- **Specific** — narrow enough to act on directly (e.g., "Book a flight to Paris," not "Handle travel")
- **Independent (where possible)** — can often be worked on without blocking on unrelated sub-goals
- **Ordered/Sequenced** — dependencies between sub-goals are clear (e.g., "Book Hotel" may depend on "Confirm Dates")
- **Measurable** — it's possible to tell when the sub-goal is complete
- **Right-sized** — not so broad it's really still a goal, not so small it's a trivial action

---

# Challenges in Goal Decomposition

Goal decomposition sounds simple but has real failure modes Agents must handle:

### Over-decomposition

Breaking a goal into too many tiny sub-goals adds overhead and can make the plan harder to follow or execute efficiently.

### Under-decomposition

Leaving sub-goals too broad (e.g., "Get a job" as a single step) means the Agent still can't act on them directly.

### Ambiguous Goals

Requests like "Make my life better" have no clear success criteria. The Agent must ask clarifying questions rather than guess.

### Hidden Dependencies

Some sub-goals silently depend on others (e.g., "Book Hotel" depends on knowing travel dates from "Book Flights"). Missing these dependencies can cause the Agent to plan steps in the wrong order.

### Conflicting Sub-goals

Sometimes sub-goals compete for the same resource (e.g., budget). The Agent should surface these trade-offs rather than silently picking one.

---

# Real-World Example

Suppose you're using an AI HR Assistant.

User Goal

```text
I want to resign from my job.
```

The Agent doesn't immediately generate a resignation letter.

Instead,

it identifies the sub-goals.

```text
Understand Notice Period

↓

Prepare Resignation Letter

↓

Return Company Assets

↓

Complete Exit Process
```

The Agent now has a clear understanding of the user's objective.

---

# Industry Insight ⭐

Modern AI Agents rarely execute a goal immediately.

Instead, they first analyze the goal to understand:

- User intent
- Constraints
- Missing information
- Success criteria

Only then do they begin planning and execution.

This produces better and more reliable results.

---

# Best Practices

Analyze the goal before creating a plan.

If important information is missing,

ask follow-up questions.

Never assume what the user wants.

When an assumption is unavoidable (e.g., no clarification is possible), state the assumption explicitly so the user can correct it later.

---

# Common Beginner Mistakes

### Mistake 1

Starting execution without understanding the goal.

This often leads to incorrect results.

---

### Mistake 2

Confusing goals with tasks.

Goals define **what** should be achieved.

Tasks define **how** to achieve it.

---

### Mistake 3

Ignoring missing information.

If the goal is unclear,

the Agent should ask for clarification before proceeding.

---

### Mistake 4

Treating decomposition as a one-time step.

Goals can change mid-execution (new information, user corrections). Mature Agents re-run Goal Analysis when context changes significantly, rather than sticking to a stale decomposition.

---

# Interview Tip ⭐

A common interview question is:

> **Why is Goal Analysis important in AI Agents?**

A good answer is:

Goal Analysis helps the Agent understand the user's intent, identify sub-goals, gather missing information, and create an effective plan before taking any action.

A strong follow-up point: Goal Analysis is typically implemented as its own LLM call or module, producing structured output (like JSON) that the Planning module consumes — it's a distinct pipeline stage, not an implicit part of planning.

---

# Where is this Used?

- OpenAI Agents SDK
- LangGraph
- LangChain
- Google ADK
- CrewAI
- AutoGen
- Enterprise AI Applications

---

# Key Takeaways

- Goal Analysis helps an Agent understand the user's objective.
- It happens before planning.
- One goal can contain multiple sub-goals.
- Clear goals lead to better planning and execution.
- Production AI Agents analyze goals before taking action.
- Goal Analysis extracts four components: intent, constraints, context, and success criteria.
- Good sub-goals are specific, ordered, measurable, and right-sized.
- Watch for over-decomposition, under-decomposition, and hidden dependencies.

---

