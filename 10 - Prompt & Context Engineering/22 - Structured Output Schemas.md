

## Learning Objectives

By the end of this chapter, you will understand:

- What a schema is
- Why schemas are important
- How schemas improve AI reliability
- How schemas differ from JSON Mode
- How to actually define a schema in code (Pydantic / JSON Schema)
- How nested objects, enums, and optional fields work
- What happens when the model produces an invalid response

---

# Introduction

In the previous chapter,

we learned about JSON Mode.

It guarantees that the response is valid JSON.

But here's a problem.

Valid JSON doesn't always mean **correct JSON**.

Example

```json
{
  "name": "John",
  "age": "Twenty"
}
```

This is valid JSON.

But should **age** be text?

No.

It should be a number.

This is where **Schemas** help.

---

# What is a Schema?

A **Schema** defines the structure of the output.

It tells the model:

- Which fields are required
- What data types to use
- What the output should look like

Think of it as a blueprint.

---

# Visual Flow

```text
Question

↓

LLM

↓

Validate Against Schema

↓

Valid Response
```

---

# Example

Schema

```text
Student

Name → String

Age → Integer

Grade → String
```

Valid Output

```json
{
  "name": "John",
  "age": 20,
  "grade": "A"
}
```

Invalid Output

```json
{
  "name": "John",
  "age": "Twenty"
}
```

The second response doesn't follow the schema.

---

# What a Schema Looks Like in Code

In practice, a schema is written using **JSON Schema** syntax, or generated from a class using a library like **Pydantic**.

Raw JSON Schema:

```json
{
  "name": "student",
  "schema": {
    "type": "object",
    "properties": {
      "name": { "type": "string" },
      "age": { "type": "integer" },
      "grade": { "type": "string", "enum": ["A", "B", "C", "D", "F"] }
    },
    "required": ["name", "age", "grade"],
    "additionalProperties": false
  },
  "strict": true
}
```

The same schema using Pydantic (Python), which most AI frameworks accept directly:

```python
from pydantic import BaseModel
from enum import Enum

class Grade(str, Enum):
    A = "A"
    B = "B"
    C = "C"
    D = "D"
    F = "F"

class Student(BaseModel):
    name: str
    age: int
    grade: Grade

response = model.create(
    messages=[{"role": "user", "content": "John is 20 and got an A."}],
    response_format=Student
)

student = Student.model_validate_json(response.content)
print(student.age)  # 20 (guaranteed to be an int, not "Twenty")
```

Notice `"strict": true` and `"additionalProperties": false` — these tell the model it **cannot** add extra fields or skip required ones. This is what actually enforces the schema, not just the field names.

---

# Nested Objects and Lists

Real-world data is rarely flat. Schemas support nesting:

```python
class Skill(BaseModel):
    name: str
    years_experience: int

class Candidate(BaseModel):
    name: str
    email: str
    skills: list[Skill]          # a list of nested objects
    linkedin: str | None = None  # an optional field
```

This lets you extract structured, hierarchical data — like a resume with multiple skills, each with its own fields — in a single call.

---

# Why Do We Need Schemas?

Imagine you're building an Employee Portal.

The AI extracts employee information.

Without a schema,

every response could be different.

With a schema,

every response follows the same structure.

This makes your application much more reliable.

---

# Real-World Example

Suppose you're building an AI Resume Parser.

Instead of asking:

```text
Extract the candidate details.
```

You define a schema.

```text
Name

Experience

Skills

Email
```

Now every response follows the same format.

---

# JSON Mode vs Schema

|JSON Mode|Schema|
|---|---|
|Ensures valid JSON|Ensures correct structure|
|Checks JSON format|Checks fields and data types|
|Doesn't validate values|Validates expected structure|

Think of it this way.

JSON Mode asks:

> **"Is this valid JSON?"**

Schema asks:

> **"Does this JSON match the expected structure?"**

---

# What Happens When Validation Fails?

Even with a schema, things can occasionally go wrong — a required field might be missing, or the model might be cut off mid-response (e.g. hitting a token limit). Always code defensively:

```python
try:
    student = Student.model_validate_json(response.content)
except ValidationError as e:
    # Log the error, and either:
    # 1. Retry the request
    # 2. Ask the model to fix the specific field
    # 3. Fall back to a default/error state
    print("Invalid response:", e)
```

With **strict mode** enabled (supported by most major providers), the model is constrained at generation time so it almost never produces an invalid structure — but your application should still handle the edge case rather than assume perfection.

---

# Where Are Schemas Used?

Schemas are used in:

- OpenAI Structured Outputs
- Pydantic AI
- LangChain
- LangGraph
- AI Agents

Almost every modern AI framework supports schemas.

---

# Best Practice

Always define a schema when:

- Another application will consume the response.
- The response contains structured data.
- Validation is important.

Don't rely on plain text when your application expects structured information.

Also:

- Keep field names descriptive — the model uses them as hints, not just labels.
- Use `enum` for fields with a fixed set of valid values instead of free text.
- Mark fields `optional` only when the data genuinely might not exist — over-using optional fields weakens reliability.

---

# Common Beginner Mistakes

### Mistake 1

Thinking JSON Mode validates the data.

It doesn't.

It only guarantees valid JSON.

---

### Mistake 2

Making the schema too complicated.

Keep schemas simple and focused.

---

### Mistake 3

Skipping validation.

Always validate AI outputs before using them in your application.

---

### Mistake 4

Assuming a schema guarantees the response will _always_ parse successfully.

Even with strict mode, always wrap parsing in error handling — truncated responses or provider-side issues can still occur.

---

### Mistake 5

Forgetting `additionalProperties: false` (or the equivalent in your framework).

Without it, the model may add extra, unexpected fields your application doesn't know how to handle.

---

# Interview Tip ⭐

A common interview question is:

> **Why use a schema if JSON Mode already exists?**

A good answer is:

JSON Mode guarantees **valid JSON**.

A Schema guarantees that the JSON contains the **correct fields and data types** required by the application.

---

# Industry Insight

Most modern AI applications no longer rely on prompt instructions like:

```text
Return JSON.
```

Instead,

they define a schema.

The framework validates the response automatically,

making AI applications far more reliable.

---

# Key Takeaways

- A schema defines the expected structure of the output.
- It validates fields and data types.
- Schemas can be written as raw JSON Schema or generated from typed classes (e.g. Pydantic).
- Schemas support nested objects, lists, enums, and optional fields for real-world data.
- `strict` mode and `additionalProperties: false` are what actually enforce the structure.
- Always handle validation errors defensively, even with strict mode enabled.
- Schemas are more reliable than prompt instructions alone.
- JSON Mode and Schemas solve different problems.
- Schemas are widely used in production AI systems.

---

