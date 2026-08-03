

## Learning Objectives

By the end of this chapter, you will understand:

- What Tool Calling is
- Why AI Agents need multiple tools
- How the model selects a tool
- How Tool Calling differs from Function Calling
- What a tool schema actually looks like in code
- How multi-step (agentic) tool calling loops work
- How to handle errors, parallel calls, and security risks

---

# Introduction

In the previous chapter,

we learned **Function Calling**.

The model could request one function like:

```text
get_weather()
```

But real AI applications don't have just one function.

They may have dozens of tools.

Example

```text
Weather Tool

Currency Tool

Calculator

Email Tool

SQL Database

Web Search
```

The next challenge is:

> **How does the AI choose the correct tool?**

This is called **Tool Calling**.

---

# What is Tool Calling?

Tool Calling is the ability of an AI model to **choose the appropriate tool** for a given task.

Instead of always calling the same function,

the model first decides:

> Which tool should I use?

---

# Visual Flow

```text
User Question

↓

LLM

↓

Choose Tool

↓

Execute Tool

↓

Tool Result

↓

Final Answer
```

---

# Example 1

User

```text
What's the weather in Delhi?
```

Available Tools

```text
Weather Tool

Calculator

Email Tool
```

The model selects:

```text
Weather Tool
```

---

# Example 2

User

```text
Calculate 25 × 48.
```

Available Tools

```text
Weather Tool

Calculator

Email Tool
```

The model selects:

```text
Calculator
```

---

# Example 3

User

```text
Send an email to John.
```

Available Tools

```text
Weather Tool

Calculator

Email Tool
```

The model selects:

```text
Email Tool
```

---

# Why is Tool Calling Important?

Without Tool Calling,

the application would need to decide which tool to use.

With Tool Calling,

the model makes that decision automatically.

This allows one AI Assistant to perform many different tasks.

---

# Tool Calling vs Function Calling

This is another common interview question.

|Function Calling|Tool Calling|
|---|---|
|Calls one specific function|Chooses from multiple tools|
|Executes the requested function|Selects the correct tool first|
|Lower-level capability|Higher-level decision making|

Think of it like this.

```text
Tool Calling

↓

Choose Calculator

↓

Function Calling

↓

calculate(25,48)
```

Tool Calling happens first.

Function Calling happens next.

---

# Real-World Example

Imagine ChatGPT.

You ask:

```text
Search today's AI news.
```

The model may choose:

```text
Web Search Tool
```

Another question

```text
Summarize this PDF.
```

Now it chooses:

```text
Document Tool
```

The user doesn't choose the tool.

The AI does.

---

# How Does the Model Choose?

The model looks at:

- The user's request
- The description of each tool
- The available parameters

Then it selects the tool that best matches the task.

---

# What a Tool Definition Actually Looks Like

Under the hood, every tool is just a JSON schema the model reads. Each tool needs:

- A **name**
- A **description** (this is what the model uses to decide _when_ to pick it)
- An **input schema** (what parameters it expects, and their types)

```json
{
  "name": "get_weather",
  "description": "Get the current weather for a given city. Use this when the user asks about temperature, rain, or general weather conditions.",
  "input_schema": {
    "type": "object",
    "properties": {
      "city": {
        "type": "string",
        "description": "The city name, e.g. 'Delhi'"
      },
      "unit": {
        "type": "string",
        "enum": ["celsius", "fahrenheit"]
      }
    },
    "required": ["city"]
  }
}
```

When the model decides to use this tool, it doesn't run any code itself. It returns a structured request like:

```json
{
  "type": "tool_use",
  "name": "get_weather",
  "input": { "city": "Delhi", "unit": "celsius" }
}
```

Your application code is responsible for:

1. Reading that request
2. Actually calling the weather API
3. Sending the result back to the model

---

# The Full Loop (Code Example)

Tool calling is not a single request/response — it's a **loop**. A simplified example:

```python
tools = [weather_tool_schema, calculator_tool_schema]

messages = [{"role": "user", "content": "What's the weather in Delhi?"}]

response = model.create(messages=messages, tools=tools)

if response.stop_reason == "tool_use":
    tool_call = response.content[-1]  # the tool_use block

    # 1. Run the real function
    result = run_tool(tool_call.name, tool_call.input)

    # 2. Send the result back to the model
    messages.append({"role": "assistant", "content": response.content})
    messages.append({
        "role": "user",
        "content": [{
            "type": "tool_result",
            "tool_use_id": tool_call.id,
            "content": str(result)
        }]
    })

    # 3. Ask the model to continue with the result
    final_response = model.create(messages=messages, tools=tools)
```

This request → tool call → tool result → final answer cycle can repeat **multiple times** if a task needs several tools in sequence (e.g. search the web, then summarize, then send an email).

---

# Parallel Tool Calls

Some models can request **more than one tool at the same time** in a single turn, instead of one at a time.

Example:

```text
User: "What's the weather in Delhi and Mumbai?"

Model returns two tool_use blocks:
  - get_weather(city="Delhi")
  - get_weather(city="Mumbai")
```

Your application should execute both, then return **both** results before asking the model for a final answer. This is faster than doing them one-by-one in separate turns.

---

# Handling Tool Errors

Tools can fail — an API might time out, or the input might be invalid. Always send the failure back to the model as a tool result instead of crashing:

```json
{
  "type": "tool_result",
  "tool_use_id": "abc123",
  "content": "Error: weather API timed out",
  "is_error": true
}
```

The model can then decide what to do next — retry, apologize to the user, or try a different tool.

---

# Security Considerations ⚠️

Tool Calling gives the model real-world power, so treat its output as **untrusted input**, not as trusted commands:

- Validate tool arguments before executing them (e.g. don't run raw SQL the model generated without checks).
- Never let a tool execute arbitrary code or shell commands directly from model output.
- Apply permission checks — a "Send Email" tool shouldn't be callable for arbitrary recipients without user confirmation.
- Watch for **prompt injection**: if a tool result (like a webpage or PDF) contains hidden instructions, the model might try to follow them. Treat tool _outputs_ as data, not as new commands.

---

# When Should You Use Tool Calling?

Whenever your AI application has multiple capabilities.

Examples

- AI Assistants
- Customer Support Bots
- Coding Assistants
- Research Agents
- Travel Planners

---

# Best Practice

Keep tools focused.

Good

```text
Weather Tool

Email Tool

Calendar Tool

SQL Tool
```

Bad

```text
Super Tool

Does Everything
```

Small, focused tools are easier for the model to understand and select correctly.

---

# Common Beginner Mistakes

### Mistake 1

Confusing Tool Calling with Function Calling.

Remember:

Tool Calling decides **which** tool to use.

Function Calling executes **that** tool.

---

### Mistake 2

Creating tools with overlapping responsibilities.

Example

```text
Weather Tool

Weather Service

Weather Helper
```

The model may struggle to choose between them.

---

### Mistake 3

Giving tools unclear descriptions.

A tool should clearly explain:

- What it does
- When it should be used
- What inputs it expects

---

### Mistake 4

Forgetting that tool calling is a **loop**, not a single call.

Beginners often send the tool result back only once and assume the conversation is done. In reality, the model may need several tool calls in sequence before it has enough information to answer.

---

### Mistake 5

Trusting tool inputs or outputs blindly.

Always validate arguments before execution, and treat tool results as data — not as instructions the model must obey.

---

# Interview Tip ⭐

A common interview question is:

> **What is the difference between Tool Calling and Function Calling?**

A good answer is:

- **Tool Calling** is the process of selecting the correct tool for a task.
- **Function Calling** is the mechanism used to execute that selected tool.

Tool Calling answers:

> **"Which tool should I use?"**

Function Calling answers:

> **"Execute this tool with these parameters."**

---

# Key Takeaways

- Tool Calling allows an AI to choose between multiple tools.
- Function Calling executes the selected tool.
- Every tool is defined by a name, description, and input schema — good descriptions drive good selection.
- Tool calling is a loop: request → tool call → tool result → (repeat) → final answer.
- Some models support parallel tool calls for efficiency.
- Always handle tool errors gracefully and validate tool inputs/outputs for security.
- Small, focused tools work better than large multi-purpose tools.
- Tool Calling is a core capability of modern AI Agents.

---

