

## Learning Objectives

By the end of this chapter, you will understand:

- What Tree of Thoughts (ToT) is
- How it differs from Chain of Thought
- When to use it
- Why it is mainly used for complex reasoning
- The actual components behind ToT: generation, evaluation, and search
- How ToT differs from Self-Consistency, even though both use "multiple paths"
- How to approximate ToT with a single prompt vs a true multi-call implementation

---

# Introduction

Suppose you're solving a maze.

You don't always choose the first path.

Instead,

you explore multiple paths.

```text
Path A

Path B

Path C
```

Then,

you choose the best one.

AI can reason in the same way.

Instead of following one line of thinking,

it explores multiple possibilities before deciding.

This is called **Tree of Thoughts (ToT)**.

---

# What is Tree of Thoughts?

Tree of Thoughts is a reasoning technique where the AI explores **multiple reasoning paths** before selecting the best solution.

Instead of thinking in one straight line,

it thinks like a tree.

---

# Visual Flow

```text
Question

        │

 ┌──────┼──────┐

 ▼      ▼      ▼

Idea A Idea B Idea C

 │       │      │

 ▼       ▼      ▼

Evaluate Each Idea

        │

        ▼

 Best Solution
```

---

# The Three Actual Components Behind ToT

The diagram above simplifies things into one round of "branch, then evaluate." The original technique is more precisely built from three distinct pieces working together, which is worth knowing for a deeper interview conversation.

```text
1. Thought Generator
   → at each step, propose several possible "next thoughts" (partial solutions,
     not necessarily complete answers)

2. State Evaluator
   → the model scores or compares these partial thoughts — e.g. "how promising
     is this path?" — often by asking the model to rate or vote on options

3. Search Algorithm
   → decides how to explore the tree using those evaluations, commonly:
     - Breadth-First Search (BFS): explore all branches at each depth level,
       keep only the most promising few, then expand those
     - Depth-First Search (DFS): follow one promising branch deeply, and
       backtrack if it turns out to be a dead end
```

This is why ToT is often described as bringing classical AI search algorithms (the kind used in pathfinding, game-playing AI, and planning) into the LLM prompting world — instead of searching a maze or game tree, the model is searching a tree of partial reasoning steps, with the LLM itself acting as both the thing generating branches and the thing scoring them.

---

# Example

User

```text
How can a company reduce costs?
```

Instead of giving the first idea,

the AI explores different options.

```text
Idea 1

Reduce operational costs.

------------

Idea 2

Automate repetitive tasks.

------------

Idea 3

Optimize supply chain.
```

After evaluating them,

the AI combines the best ideas into the final answer.

---

# Why Does It Work?

Sometimes,

the first idea isn't the best one.

Tree of Thoughts encourages the model to explore different possibilities before making a decision.

This often produces better solutions for complex problems.

---

# Tree of Thoughts vs Self-Consistency — Don't Confuse Them

Both techniques involve "generating multiple things and picking the best," which makes them easy to conflate. The mechanism is actually quite different, and this is a strong interview differentiator.

```text
Self-Consistency
→ Generate several COMPLETE, independent reasoning paths from start to finish
→ No evaluation happens until the very end
→ The final answers are compared, and the most common one wins (majority vote)
→ Each path doesn't know about or influence the others while running

Tree of Thoughts
→ Generate PARTIAL thoughts (one step at a time), evaluate them as you go
→ Weak branches can be abandoned early (pruned) before being fully completed
→ The search actively steers toward promising branches, rather than running
  everything to completion and voting afterward
```

In short: Self-Consistency evaluates only at the finish line; Tree of Thoughts evaluates continuously and can cut off bad paths early, which makes it more computationally efficient for problems with a large branching space — but also more complex to implement, since it requires an evaluation step and a search strategy, not just N independent generations.

---

# Chain of Thought vs Tree of Thoughts

|Chain of Thought|Tree of Thoughts|
|---|---|
|One reasoning path|Multiple reasoning paths|
|Linear thinking|Branching thinking|
|Faster|More expensive|
|Simpler|Better for complex problems|

Think of it this way.

Chain of Thought

```text
A

↓

B

↓

C
```

Tree of Thoughts

```text
        A

   ↙    ↓    ↘

  B1   B2    B3

    ↘   ↓   ↙

     Best Answer
```

---

# Approximating ToT With a Single Prompt vs a True Multi-Call Implementation

Worth being clear that there are two very different levels of "doing ToT":

```text
Single-prompt approximation
→ One prompt asking the model to generate several options, briefly evaluate
  each in its own reasoning, and then pick the best — all within one response
→ Easy to try, cheap, but the "evaluation" and "search" are really just the
  model reasoning about it in one pass, not a true structured search

True multi-call implementation
→ Separate calls for generating thoughts, separate calls (or the same call)
  for scoring them, and application code that implements the actual BFS/DFS
  search logic, deciding which branches to expand or discard
→ Closer to the original technique, more controllable, but significantly
  more expensive and complex to build
```

Most people who say they've "used Tree of Thoughts" in a simple project mean the single-prompt approximation — worth being precise about which version you mean if this comes up in an interview.

---

# When Should You Use It?

Use Tree of Thoughts for:

- Strategy
- Planning
- Research
- Design problems
- Complex decision making

These tasks usually have multiple possible solutions.

---

# When Should You Avoid It?

Don't use it for simple questions.

Example

```text
What is Java?
```

There is no need to explore multiple reasoning paths.

Tree of Thoughts would only increase:

- Cost
- Latency
- Complexity

---

# Industry Reality ⭐

Tree of Thoughts is an important research idea,

but it is **not commonly used in everyday production AI systems**.

Most production applications prefer:

- ReAct
- Plan-and-Execute
- Reflexion

because they are simpler, faster, and less expensive.

However,

understanding Tree of Thoughts helps you understand how advanced reasoning systems are designed.

---

# Best Practice

Use Tree of Thoughts only when:

- There are multiple valid solutions.
- Choosing the best solution is important.
- The extra computation is justified.

---

# Common Beginner Mistakes

### Mistake 1

Thinking Tree of Thoughts replaces Chain of Thought.

It doesn't.

It extends the idea by exploring multiple reasoning paths.

---

### Mistake 2

Using Tree of Thoughts for every task.

Most real-world applications don't need it.

---

### Mistake 3

Ignoring the cost.

Exploring multiple branches requires more computation,

making it slower and more expensive.

---

### Mistake 4

Confusing Tree of Thoughts with Self-Consistency because both use "multiple paths."

Self-Consistency evaluates complete paths only at the end; ToT evaluates and prunes partial paths as it goes.

---

# Interview Tip ⭐

A common interview question is:

> **What is the difference between Chain of Thought and Tree of Thoughts?**

A good answer is:

- **Chain of Thought** follows one reasoning path.
- **Tree of Thoughts** explores multiple reasoning paths, evaluates them, and selects the best one.

Tree of Thoughts is more suitable for complex planning and decision-making problems.

---

# Interview Tip ⭐

A stronger follow-up worth preparing for:

> **How is Tree of Thoughts different from Self-Consistency, since both involve generating multiple things?**

Answer:

Self-Consistency generates several complete, independent reasoning paths and only compares them at the very end via majority vote. Tree of Thoughts generates and evaluates partial thoughts as it goes, using a search strategy (like BFS or DFS) to prune weak branches early rather than running every path to completion. ToT is more computationally efficient for large search spaces but more complex to implement, since it needs an explicit evaluation and search mechanism, not just repeated independent generation.

---

# Key Takeaways

- Tree of Thoughts explores multiple reasoning paths.
- It's built from three components: a thought generator, a state evaluator, and a search algorithm (commonly BFS or DFS).
- It brings classical AI search concepts into LLM prompting — the model both generates and scores its own branches.
- It differs from Self-Consistency: ToT evaluates and prunes continuously; Self-Consistency evaluates only at the end via majority vote.
- A single-prompt version is a rough approximation; a true implementation requires separate generation, evaluation, and search logic.
- It is useful for complex decision-making tasks.
- It is more expensive than Chain of Thought.
- Most production systems prefer simpler reasoning patterns unless multiple alternatives are truly needed.
- Understanding Tree of Thoughts helps explain advanced reasoning techniques used in AI research.

---

