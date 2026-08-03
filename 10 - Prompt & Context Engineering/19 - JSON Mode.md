

## Learning Objectives

By the end of this chapter, you will understand:

- What JSON Mode is
- Why it is better than asking for JSON in a prompt
- When to use it
- Its limitations
- How JSON Mode is actually implemented under the hood (constrained decoding)
- Why "valid JSON" and "correct JSON" are two different guarantees
- Why you still need schema validation even with JSON Mode enabled

---

# Introduction

In the previous chapter,

we asked the model to return JSON.

Example

```text
Return the response as JSON.
```

Usually it works.

But sometimes,

the model may return:

```text
Here is the JSON:

{
   ...
}
```

or

````text
```json
{
   ...
}
````

````

This creates problems for applications.

Modern LLM APIs solve this using **JSON Mode**.

---

# What is JSON Mode?

JSON Mode is an API feature that tells the model:

> **Return only valid JSON.**

Instead of relying on the prompt,

the API enforces the response format.

---

# How Is This Actually Enforced? (Constrained Decoding)

It's worth understanding *how* this guarantee is technically achieved, since it connects directly back to how generation works, covered in an earlier chapter.

Recall that generation is autoregressive — at each step, the model predicts a probability distribution over possible next tokens, and one gets sampled. Normally, *any* token the model considers likely enough can be chosen.

JSON Mode works by restricting that choice at the token level:

```text
Normal generation:  model can pick any plausible next token
                     (including "Sure!", punctuation, extra words, etc.)

JSON Mode:           at each step, only tokens that would keep the output
                     valid JSON syntax are allowed to be chosen at all —
                     invalid tokens are filtered out before sampling happens
````

This general mechanism is called **constrained** or **grammar-constrained decoding** — the model isn't being asked nicely to produce valid syntax, its actual choices are mechanically limited so it structurally cannot produce invalid JSON syntax. This is the real difference from Structured Outputs as a prompting technique: prompting shapes _what the model wants to generate_; JSON Mode shapes _what the model is allowed to generate_ at each step.

---

# Without JSON Mode

Prompt

```text
Return student information as JSON.
```

Possible Response

```text
Sure!

{
  "name":"John",
  "age":20
}
```

Notice the extra text.

This may break your application.

---

# With JSON Mode

Response

```json
{
  "name": "John",
  "age": 20
}
```

Only JSON is returned.

---

# "Valid JSON" Is Not the Same as "Correct JSON"

This is the single most important limitation to understand about JSON Mode, and a very common interview trap.

JSON Mode guarantees the output will **parse** as JSON — correct brackets, correct commas, correct quoting. It does **not** guarantee:

```text
- The fields you expected are actually present
- Field names are spelled/named the way you wanted
- Field types are correct (e.g. age returned as "20" the string,
  instead of 20 the number)
- Values are factually accurate
- Required fields aren't silently omitted or hallucinated
```

Example of technically valid but practically wrong output:

```json
{
  "student_name": "John",
  "yrs_old": "twenty"
}
```

This is syntactically perfect JSON — it will parse without error — but it doesn't match the schema you actually needed (`name`, `age` as a number). JSON Mode alone can't catch this, because syntax validity and schema correctness are two entirely different guarantees.

---

# Why Is Important?

Imagine your application does this.

```python
response = llm()

data = json.loads(response)
```

If the model returns:

```text
Sure! Here's the JSON...
```

Your program may fail.

JSON Mode reduces this problem.

---

# You Still Need Schema Validation

Because JSON Mode only guarantees syntax, not structure, production applications should still validate the parsed data against an expected schema — typically using a library like Pydantic (Python) or a JSON Schema validator.

```text
1. Get response (guaranteed to be syntactically valid JSON, thanks to JSON Mode)

↓

2. Parse it into a data structure

↓

3. Validate against your expected schema (required fields present,
   correct types, values within expected ranges)

↓ If validation fails
4. Retry with the validation error fed back to the model — same
   pattern discussed in the Structured Outputs chapter
```

This is the layered approach worth remembering: JSON Mode handles the "does this even parse" problem; schema validation handles the "does this match what I actually need" problem. Later chapters covering schema-constrained function calling take this a step further by constraining the model to a specific schema, not just valid JSON syntax in general — worth keeping in mind as you move forward.

---

# Real-World Example

Suppose you're building an AI Resume Parser.

Input

```text
John

Java Developer

5 Years Experience
```

Response

```json
{
  "name": "John",
  "role": "Java Developer",
  "experience": 5
}
```

The application can directly store this in a database.

---

# Structured Outputs vs JSON Mode

|Structured Outputs|JSON Mode|
|---|---|
|Prompting technique|API feature|
|Requests JSON|Enforces valid JSON|
|Model may still make formatting mistakes|Much more reliable|

Think of it like this.

```text
Structured Output

↓

"Please return JSON."
```

```text
JSON Mode

↓

"You MUST return JSON."
```

---

# When Should You Use JSON Mode?

Use it when:

- Building APIs
- Calling backend services
- Storing data in databases
- AI Agents
- Automation workflows

Anywhere software needs predictable responses.

---

# When Should You Avoid It?

Don't use JSON Mode for:

- Blogs
- Emails
- Stories
- Conversations

These are better returned as normal text.

---

# Best Practice

Use JSON Mode whenever another program will consume the AI response.

It is much more reliable than depending only on prompt instructions.

Still pair it with schema validation — JSON Mode solves syntax, not structure or correctness.

---

# Common Beginner Mistakes

### Mistake 1

Thinking JSON Mode understands your data structure.

It only guarantees valid JSON.

You still need to specify the required fields.

---

### Mistake 2

Using JSON Mode for normal conversations.

It's designed for structured applications,

not chat responses.

---

### Mistake 3

Confusing JSON Mode with Structured Outputs.

Remember:

- **Structured Outputs** describe the desired format.
- **JSON Mode** enforces valid JSON.

---

### Mistake 4

Assuming "valid JSON" means "correct data."

JSON Mode guarantees parseable syntax only — it does not guarantee the right fields, types, or accurate values. Schema validation is still necessary.

---

# Interview Tip ⭐

A common interview question is:

> **What is the difference between Structured Outputs and JSON Mode?**

A good answer is:

- **Structured Outputs** are a prompting technique where we ask the model to return structured data.
- **JSON Mode** is an API capability that makes the model return valid JSON, making it much more reliable for applications.

---

# Interview Tip ⭐

A stronger follow-up worth preparing for:

> **Does JSON Mode guarantee that the response matches your expected schema?**

Answer:

No. JSON Mode guarantees syntactically valid JSON — parseable brackets, quotes, and commas — using constrained decoding, which restricts the model's token choices at each generation step. It does not guarantee the correct fields, field names, types, or accurate values. You still need schema validation (e.g. with Pydantic or JSON Schema) on top of JSON Mode to catch structural or correctness issues.

---

# Key Takeaways

- JSON Mode is an API feature.
- It guarantees valid JSON responses using constrained (grammar-restricted) decoding, which limits what tokens the model can generate at each step.
- Valid JSON is not the same as correct JSON — JSON Mode doesn't guarantee the right fields, types, or accurate values.
- Schema validation (e.g. Pydantic) is still necessary on top of JSON Mode for real reliability.
- It is more reliable than asking for JSON in a prompt.
- It is ideal for APIs, AI Agents, and automation.
- JSON Mode works best when another application will process the response.

---

