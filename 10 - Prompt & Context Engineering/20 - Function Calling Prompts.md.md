

## Learning Objectives

By the end of this chapter, you will understand:

- What Function Calling is
- Why it is important
- How it works
- How it differs from ReAct
- How the model actually knows which functions exist and how to call them
- Why you should never blindly execute a function call without validation
- What parallel function calling is
- How "Function Calling" and "Tool Calling" relate as terms

---

# Introduction

Suppose you ask an AI:

```text
What's the weather in Mumbai today?
```

The AI knows it cannot answer from memory.

Instead of guessing,

it should call a Weather API.

But how does the AI tell the application:

> "Please call the Weather API."

This is where **Function Calling** comes in.

---

# What is Function Calling?

Function Calling is an API capability that allows the model to request the execution of a predefined function.

The model **does not execute the function itself**.

Instead,

it tells your application:

```text
Call this function

with these parameters.
```

Your application executes it and returns the result.

---

# How Does the Model Know What Functions Exist?

This is the missing piece that makes the whole mechanism click: the model isn't magically aware of your application's functions. You have to describe them, typically as structured schema definitions sent alongside your prompt in the API request.

```json
{
  "name": "get_weather",
  "description": "Get the current weather for a given city",
  "parameters": {
    "type": "object",
    "properties": {
      "city": {
        "type": "string",
        "description": "The city name, e.g. Mumbai"
      }
    },
    "required": ["city"]
  }
}
```

This is sent to the model as part of the request — not something the model already knows. When the model decides a function is needed, it generates output matching this schema (usually using constrained decoding, the same underlying mechanism discussed in the JSON Mode chapter, so the function name and arguments come back in a reliably parseable shape). Better function _descriptions_ generally lead to better decisions about _when_ and _how_ to call them — this is effectively a form of prompt engineering applied to tool definitions, not just user-facing text.

---

# Visual Flow

```text
User Question

↓

LLM

↓

Function Request

↓

Application

↓

Execute Function

↓

Function Result

↓

LLM

↓

Final Answer
```

---

# Example

User

```text
What's the weather in Mumbai?
```

The model returns:

```text
Call Function:

get_weather(

    city="Mumbai"
)
```

Your application calls the Weather API.

API Response

```text
28°C

Cloudy
```

The application sends the result back to the model.

Final Answer

```text
The current weather in Mumbai is 28°C and cloudy.
```

---

# Another Example

User

```text
Convert 100 USD to INR.
```

Model

```text
Call Function

convert_currency(

    from="USD",

    to="INR",

    amount=100
)
```

The application executes the function.

The model then generates the final answer.

---

# Never Blindly Execute What the Model Requests

This is a critical production and security point, and connects to the prompt injection concept.

The model can:

```text
- Hallucinate parameters that don't make sense (a negative amount,
  a malformed city name, an out-of-range ID)
- Be manipulated by malicious content in the conversation or in
  retrieved documents into requesting a harmful or unintended function
  call (this is a form of prompt injection — untrusted text convincing
  the model to take an action it shouldn't)
- Request a function call with the right shape but wrong intent
  (e.g. requesting delete_user() when the user only asked a question
  about their account)
```

Because of this, your application code should treat every function call request from the model as **untrusted input that needs validation**, not as a command to execute automatically:

```text
1. Validate the requested parameters (types, ranges, allowed values)
2. Apply the same authorization checks you'd apply to any user action
   (does this user actually have permission to do this?)
3. For sensitive/destructive actions (payments, deletions, sending
   messages), consider requiring explicit human confirmation before
   executing
4. Log every function call request and execution for auditability
```

This matters more as the function's real-world impact increases — reading a weather API is low-risk; calling a "delete_account" or "transfer_funds" function is not, and deserves stricter guardrails.

---

# Parallel Function Calling

Modern LLM APIs often support requesting **multiple function calls in a single turn**, rather than strictly one-at-a-time.

```text
User: "What's the weather in Mumbai and Delhi?"

Model requests both in one response:
  get_weather(city="Mumbai")
  get_weather(city="Delhi")

↓

Application executes both (potentially in parallel)

↓

Both results are returned to the model

↓

Model combines them into one final answer
```

This reduces round-trips and latency compared to calling the model once per function, waiting for each result before deciding on the next call — useful to know as a practical optimization when designing tool-using systems.

---

# Why is Function Calling Important?

Without Function Calling,

the model can only generate text.

With Function Calling,

it can interact with the real world.

Examples

- Weather APIs
- Databases
- Email services
- Payment systems
- Booking systems
- Internal company tools

---

# Function Calling vs ReAct

This is one of the most common interview questions.

|ReAct|Function Calling|
|---|---|
|Reasoning pattern|API capability|
|Decides **whether** a tool is needed|Requests execution of the tool|
|"I should check the weather."|"Call get_weather()"|

Think of it like this.

ReAct is the **brain**.

Function Calling is the **hands**.

---

# A Note on Terminology: "Function Calling" vs "Tool Calling"

Heads up before the next chapter: in casual usage across the industry, "Function Calling" and "Tool Calling" are often used interchangeably — you'll see both terms describing the same underlying capability. Some providers historically used "function calling" as the specific API term, with "tool calling" (or just "tools") becoming a more general, provider-agnostic term as the ecosystem grew to include things beyond simple functions (like web search or code execution as built-in "tools"). This book draws a more specific distinction in the next chapter (calling one known function vs. choosing among many available tools) — just be aware that in real-world conversations and job descriptions, people frequently use the two terms loosely and interchangeably.

---

# Real-World Example

Suppose you're building an AI Banking Assistant.

User

```text
What's my account balance?
```

The model cannot answer directly.

Instead,

it requests:

```text
get_account_balance(

    customer_id=123
)
```

The banking system executes the function,

and the model explains the result to the user.

---

# When Should You Use Function Calling?

Use it whenever the AI needs to:

- Fetch live information
- Execute business logic
- Access databases
- Use external APIs
- Trigger actions

---

# When Should You Avoid It?

Don't use Function Calling when the model already knows the answer.

Example

```text
What is Java?
```

No function is needed.

The model can answer directly.

---

# Best Practice

Functions should do **one job only**.

Good

```text
get_weather()
```

```text
send_email()
```

```text
search_customer()
```

Avoid creating one function that performs many unrelated tasks.

Also write clear, specific function descriptions — the model relies on them to decide when and how to call each function, the same way it relies on clear instructions in a regular prompt.

---

# Common Beginner Mistakes

### Mistake 1

Thinking the model executes functions.

It doesn't.

Your application executes them.

---

### Mistake 2

Using Function Calling for simple questions.

Only use functions when external information or actions are required.

---

### Mistake 3

Confusing Function Calling with ReAct.

Remember:

- ReAct decides.
- Function Calling executes.

---

### Mistake 4

Executing a requested function call without validating its parameters or checking authorization.

Treat every function call request as untrusted input, especially for sensitive or destructive actions.

---

# Interview Tip ⭐

A very common interview question is:

> **Does the LLM execute the function?**

Answer:

No.

The LLM only returns a **function call request**.

Your application receives that request,

executes the function,

and sends the result back to the model.

---

# Interview Tip ⭐

A strong follow-up worth preparing for:

> **What security considerations apply when using Function Calling?**

Answer:

Every function call request from the model should be treated as untrusted input — validate parameters, apply the same authorization checks as any user action, and require confirmation for sensitive or destructive operations. This matters especially because malicious content in the conversation or retrieved documents could attempt to manipulate the model into requesting harmful function calls (a form of prompt injection).

---

# Key Takeaways

- Function Calling is an API capability.
- The model is told which functions exist via structured schema descriptions sent with the request — it doesn't know about them by default.
- The LLM requests function execution; it never executes anything itself.
- Your application executes the function — and should validate and authorize every request first, since the model's output can be wrong or manipulated.
- Modern APIs often support parallel function calls in a single turn, reducing round-trips.
- "Function Calling" and "Tool Calling" are often used interchangeably in the industry, even where a book draws a sharper distinction between them.
- Function Calling enables AI to interact with external systems.
- It is a core building block of modern AI Agents.

---

