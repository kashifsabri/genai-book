

## Learning Objectives

By the end of this chapter, you will understand:

- The basic parts of a prompt
- Why each part is important
- How to write a clear prompt
- How to avoid vague prompts
- How to use delimiters to separate parts of a prompt clearly
- What order to prioritize the five parts in when token budget is tight
- How to debug a weak prompt using this framework

---

# Introduction

Many beginners think a prompt is just a question.

It isn't.

A good prompt is made up of different parts.

The more information you provide,

the easier it is for the LLM to understand your request.

---

# The 5 Parts of a Prompt

A well-written prompt usually contains:

```text
Role

↓

Task

↓

Context

↓

Constraints

↓

Output Format
```

Not every prompt needs all five,

but most production prompts use them.

---

# 1. Role

Tell the model **who it should act as**.

Example

```text
You are a Java Developer.
```

```text
You are a Financial Advisor.
```

```text
You are a Data Analyst.
```

The role helps the model respond from the correct perspective.

---

# 2. Task

Tell the model **what to do**.

Example

```text
Explain Spring Boot.
```

```text
Summarize this document.
```

```text
Translate this paragraph into Hindi.
```

Every prompt should have one clear task.

---

# 3. Context

Provide the necessary background.

Example

```text
The audience is a beginner.
```

```text
This code is part of an Employee Management System.
```

Context helps the model make better decisions.

---

# 4. Constraints

Tell the model what it should or shouldn't do.

Examples

```text
Use simple English.
```

```text
Keep the answer under 100 words.
```

```text
Do not use technical jargon.
```

Constraints reduce ambiguity.

---

# 5. Output Format

Tell the model how you want the answer.

Examples

```text
Return JSON.
```

```text
Use bullet points.
```

```text
Return only Python code.
```

Never assume the model knows your preferred format.

---

# Example 1 — Poor Prompt

```text
Tell me about Docker.
```

Problems

- No audience
- No length
- No format
- Too vague

---

# Example 2 — Better Prompt

```text
You are a DevOps instructor.

Explain Docker to a beginner.

Use simple English.

Limit the answer to 150 words.

Use bullet points.
```

Notice how much clearer this is.

---

# Visual Flow

```text
Role

↓

Task

↓

Context

↓

Constraints

↓

Output
```

Each layer helps the model produce a better answer.

---

# Real-World Example

Suppose you're building an AI coding assistant.

Instead of:

```text
Write Java code.
```

Use:

```text
You are a Senior Java Developer.

Write a Spring Boot REST API for Employee CRUD.

Use Java 21.

Follow REST best practices.

Return only code.
```

The second prompt is much more useful.

---

# Using Delimiters to Separate Prompt Sections

As prompts grow longer — especially once you add context, documents, or examples — it becomes important to clearly mark where each part starts and ends. Otherwise the model can confuse your instructions with your context, or your context with the user's actual question.

Common delimiter styles:

```text
XML-style tags
<role>You are a Senior Java Developer.</role>
<task>Write a Spring Boot REST API for Employee CRUD.</task>
<constraints>Use Java 21. Return only code.</constraints>
```

```text
Markdown headers
## Role
You are a Senior Java Developer.

## Task
Write a Spring Boot REST API for Employee CRUD.

## Constraints
Use Java 21. Return only code.
```

```text
Triple quotes / fences for pasted content
Summarize the article below.

Article:
"""
<pasted article text>
"""
```

This matters most when a prompt includes pasted content (documents, code, user data) — without a clear delimiter, the model can mistake pasted text for part of your instructions. Some models (Claude in particular) are explicitly trained to respond well to XML-style tags for this reason.

---

# Priority Order When Token Budget is Tight

Not every prompt has room for all five parts, especially inside an application with a fixed token budget. If you have to cut something, this is a reasonable priority order:

```text
1. Task         → never optional, the model needs to know what to do
2. Constraints   → prevents the most common failure modes (wrong length, wrong tone)
3. Output Format → prevents having to re-parse or re-ask
4. Context       → helps accuracy, but the model can sometimes infer it
5. Role          → nice to have, but the weakest lever of the five
```

This is a generalization, not a rule — for tasks like customer support or tone-sensitive writing, Role and Context may matter more than this ordering suggests. But when in doubt, Task and Constraints are the two parts worth protecting first.

---

# Debugging a Weak Prompt With This Framework

When a prompt isn't producing good output, use the five parts as a checklist to diagnose _why_, instead of randomly rewording it.

```text
Output is generic / shallow        → missing Role or Context
Output is the wrong length/tone     → missing Constraints
Output is in the wrong shape        → missing Output Format
Output solves the wrong problem     → Task itself is unclear or too broad
Output ignores earlier instructions → too much unrelated info, or poor use of delimiters
```

This turns "the prompt isn't working" into a specific, fixable diagnosis rather than trial and error.

---

# Best Practice

Before sending a prompt,

ask yourself these questions.

✅ Who should the model act as?

✅ What is the task?

✅ Does it have enough context?

✅ Are there any constraints?

✅ What output format do I want?

If you can answer all five,

your prompt is usually well-designed.

---

# Common Beginner Mistakes

### Mistake 1

Giving multiple unrelated tasks.

Bad

```text
Explain Java.

Write Python code.

Translate to French.
```

Focus on one task at a time.

---

### Mistake 2

Not specifying the audience.

An explanation for a beginner is very different from one for an experienced developer.

---

### Mistake 3

Forgetting the output format.

If you don't specify it,

the model chooses one for you.

---

### Mistake 4

Adding unnecessary details.

Only include information that helps the model complete the task.

---

### Mistake 5

Mixing instructions and pasted content with no delimiter.

If you paste a document, code, or user data directly into the prompt without marking its boundaries, the model can confuse it with your instructions.

---

# Quick Self-Check Questions

- If a model's output keeps ignoring your word limit, which of the five parts is most likely missing or weak?
- Why do delimiters matter more as a prompt gets longer or includes pasted content?
- If you only had room for two of the five parts, which two would generally give you the most reliability, and why?

---

# Key Takeaways

- A good prompt has five parts:
    - Role
    - Task
    - Context
    - Constraints
    - Output Format
- Clear prompts produce better results.
- Don't assume the model knows your intent.
- Always specify the output format when it matters.
- Use delimiters (XML tags, markdown headers, triple quotes) to separate instructions from pasted content, especially in longer prompts.
- When token budget is tight, Task and Constraints are generally worth protecting first.
- Use the five parts as a diagnostic checklist when a prompt isn't producing good output.

---

