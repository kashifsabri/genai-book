

## Learning Objectives

By the end of this chapter, you will understand:

- What Structured Outputs are
- Why they are important
- How they differ from plain text
- When to use them
- Why asking for JSON in plain instructions is only a "soft guarantee"
- Why autoregressive generation makes formatting mistakes easy to happen
- How to validate and recover from malformed structured output in a real application

---

# Introduction

Suppose you ask an AI:

```text
Give me details about a student.
```

The AI might reply:

```text
The student's name is John.

He is 20 years old.

His grade is A.
```

This is easy for humans to read.

But can another program easily use this response?

Not always.

Programs work better with **structured data**.

---

# What are Structured Outputs?

Structured Outputs mean asking the AI to return data in a predefined format instead of free-form text.

For example,

instead of:

```text
John is 20 years old.
```

return:

```json
{
  "name": "John",
  "age": 20,
  "grade": "A"
}
```

Now the response is easy for both humans and programs.

---

# Why Do We Need Structured Outputs?

Imagine you're building an AI application.

The AI extracts customer information.

Without structure,

every response may look different.

Example 1

```text
John is 20 years old.
```

Example 2

```text
Age: 20

Name: John
```

Example 3

```text
Student Name - John

Grade - A
```

This is difficult for software to process.

Structured Outputs solve this problem.

---

# Example

Prompt

```text
Extract the following information.

Return the result as JSON.

Name: John

Age: 20

Grade: A
```

Response

```json
{
  "name": "John",
  "age": 20,
  "grade": "A"
}
```

---

# This Is Only a Soft Guarantee

This is the most important thing to understand before the next chapter.

When you write "Return the result as JSON" as a plain instruction, you're **asking nicely** — the model is still generating this as free text, token by token, and nothing is technically stopping it from:

```text
- Adding a sentence before the JSON ("Sure, here's the data:")
- Adding a sentence after the JSON ("Let me know if you need anything else!")
- Producing almost-valid JSON (missing a comma, an extra trailing comma,
  mismatched brackets, using single quotes instead of double quotes)
- Wrapping the JSON in a markdown code fence your parser doesn't expect
```

This connects directly back to how generation actually works: since output is autoregressive (each token depends on everything generated before it), a single small mistake early on — like starting with a stray character — has no built-in mechanism forcing the model to "notice" and self-correct. The model is pattern-matching to what JSON usually looks like, not running an actual JSON validator on its own output as it goes.

This is exactly the gap that dedicated features like **JSON Mode** and **schema-constrained generation** (covered in the next chapters) are built to close — they move the guarantee from "the model was asked nicely" to "the output is mechanically constrained to be valid."

---

# Common Formats

JSON

```json
{
  "name": "John"
}
```

---

List

```text
- Apple

- Banana

- Mango
```

---

Table

|Name|Age|
|---|--:|
|John|20|

Choose the format that best suits your application.

---

# Real-World Example

Suppose you're building an AI Resume Parser.

Instead of:

```text
John is a Java Developer with 5 years of experience.
```

Return:

```json
{
  "name": "John",
  "skill": "Java",
  "experience": 5
}
```

Now your application can directly store this in a database.

---

# Handling Malformed Output in Real Applications

Since prompted JSON is only a soft guarantee, production systems need a plan for when the model doesn't comply perfectly. A common, practical pattern:

```text
1. Get the model's response

2. Try to parse it as JSON (or validate against a schema, e.g. using
   Pydantic in Python)

↓ If parsing succeeds
3. Use the data

↓ If parsing fails
4. Send the malformed response and the parser's error message BACK to
   the model, asking it to fix the formatting

5. Retry parsing (usually with a retry limit, e.g. 2-3 attempts, to avoid
   an infinite loop — the same concern raised in the ReAct chapter)
```

Many teams use schema libraries (like Pydantic in Python) not just to validate the final structure, but to auto-generate the schema description that gets included in the prompt in the first place — keeping the prompt's stated structure and the code's actual expected structure in sync.

---

# Nested and Complex Schemas

Real-world structured extraction often isn't as flat as a single name/age/grade object. Nested structures are common:

```json
{
  "name": "John",
  "skills": ["Java", "Spring Boot", "SQL"],
  "experience": {
    "years": 5,
    "companies": [
      {"name": "TechCorp", "role": "Backend Developer"},
      {"name": "DataSoft", "role": "Software Engineer"}
    ]
  }
}
```

The deeper and more nested the required structure, the more room there is for the model to make a small formatting mistake somewhere inside it — which is another reason schema-enforced generation (next chapters) becomes increasingly valuable as structures get more complex, rather than relying on instructions alone.

---

# When Should You Use Structured Outputs?

Use them when the AI response will be consumed by another application.

Examples

- APIs
- Databases
- Dashboards
- Automation
- AI Agents

---

# When Should You Avoid Them?

If you're writing:

- Blogs
- Emails
- Stories
- Articles

Plain text is usually more natural.

---

# Best Practice

Always define the expected format.

Bad

```text
Extract the information.
```

Better

```text
Extract the information.

Return JSON with:

- name
- age
- grade
```

Never assume the model knows your desired structure.

---

# Common Beginner Mistakes

### Mistake 1

Asking for structured data without specifying the format.

Always tell the model what structure you expect.

---

### Mistake 2

Using structured outputs for everything.

Not every task needs JSON.

Sometimes plain text is the better choice.

---

### Mistake 3

Expecting every response to follow the format perfectly.

Older models may occasionally make formatting mistakes.

Modern APIs provide stronger guarantees, which we'll learn next.

---

### Mistake 4

Building an application that assumes prompted JSON will always parse successfully, with no fallback for malformed output.

Always include parsing validation and a retry path in production code.

---

# Interview Tip ⭐

A common interview question is:

> **Why are Structured Outputs important?**

A good answer is:

Structured Outputs make AI responses predictable and machine-readable, allowing applications to reliably process, validate, and store the generated data.

---

# Interview Tip ⭐

A stronger follow-up worth preparing for:

> **If you ask a model to "return JSON" in your prompt, is that guaranteed to always be valid JSON? Why or why not?**

Answer:

No — that's only a soft guarantee, since the model is generating the response as free text token by token, with nothing mechanically enforcing JSON validity. Small mistakes (extra text, a missing comma, wrong quote style) can still occur. Reliable production systems either use a dedicated feature like JSON Mode or schema-constrained generation, or validate and retry when prompted JSON alone isn't enforced.

---

# Key Takeaways

- Structured Outputs return data in a predefined format.
- They are easier for applications to process than plain text.
- Asking for JSON through plain instructions is a soft guarantee, not a hard one — the model can still produce invalid or extra-wrapped output.
- This gap exists because generation is autoregressive text generation, not validated data generation.
- Production systems should validate structured output and retry (with the error fed back to the model) rather than assume it always parses.
- Nested/complex schemas increase the chance of small formatting mistakes, making stronger guarantees more valuable.
- JSON is the most commonly used format.
- They are essential for APIs, automation, and AI Agents.
- Always specify the expected output structure.

---

