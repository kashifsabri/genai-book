

## Learning Objectives

By the end of this chapter, you will understand:

- What Prompt Sandboxing is
- Why it is important
- How it limits the impact of AI mistakes
- Why it is widely used in AI Agents
- The two distinct kinds of sandboxing: capability restriction vs execution isolation
- How sandboxing is actually implemented in code (scoped tools, permissions)
- How to handle sensitive actions that need more than a blanket allow/deny

---

# Introduction

Imagine giving a new employee access to your company.

Would you immediately give them:

- The production database
- Customer records
- Payment systems

No.

You would first give them limited access.

AI Agents follow the same principle.

Instead of giving the AI unrestricted access,

we place it inside a **sandbox**.

---

# What is Prompt Sandboxing?

Prompt Sandboxing is the practice of limiting what an AI can access or do, even if it receives malicious or incorrect instructions.

The goal is simple:

> **Limit the damage if something goes wrong.**

---

# Example

Suppose an AI can use three tools.

```text
Calculator

Weather API

Email Sender
```

If the AI is answering a math question,

it should only use:

```text
Calculator
```

It should **not** have access to the Email Sender.

---

# Visual Flow

```text
User Request

↓

LLM

↓

Sandbox Rules

↓

Allowed Tools Only

↓

Response
```

The sandbox decides what the AI is allowed to access.

---

# Two Different Kinds of Sandboxing

"Sandboxing" is a broad term. In practice, it covers two related but distinct techniques, and production systems usually need both:

### 1. Capability Sandboxing (which tools/permissions are available)

This is what most of this chapter focuses on — restricting _which tools an agent can even call_, and with _what scope_, regardless of what the model decides to attempt.

```python
# Each agent gets a narrow, purpose-built set of tools
weather_agent_tools = [get_weather]
hr_agent_tools = [read_hr_policy, view_leave_balance]  # no delete/edit tools at all

# A scoped API key limits reach even within an allowed tool
email_tool_config = {
    "api_key": scoped_key,           # can only send, not delete
    "allowed_recipients": "@company.com",  # can't send externally
    "rate_limit": "10 per hour"
}
```

### 2. Execution Sandboxing (isolating code the AI runs)

When an agent can execute code (e.g. a coding assistant, a data-analysis agent running Python), the code itself needs to run in an isolated environment — a container or VM with no access to the host filesystem, network, or credentials it doesn't need.

```text
AI generates code
    ↓
Code runs inside a locked-down container
    ↓
- No access to host filesystem
- No outbound network access (unless explicitly allowed)
- Limited CPU/memory/time
- Destroyed after execution
```

This matters even if the _code itself_ looks harmless, because a jailbroken or injected agent could otherwise generate code that reads secrets, makes unauthorized network calls, or affects the host system directly. Capability sandboxing controls _what the agent is allowed to ask for_; execution sandboxing controls _what happens if code actually runs_.

---

# Real-World Example

Imagine an AI HR Assistant.

It can:

- Read HR policies ✅
- View employee leave balances ✅

But it **cannot**:

- Delete employee records ❌
- Change salaries ❌
- Access payroll ❌

Even if someone tries Prompt Injection,

the AI simply doesn't have permission.

---

# Why is Prompt Sandboxing Important?

LLMs can make mistakes.

Users can also provide malicious prompts.

Sandboxing reduces the impact by limiting what the AI can do.

Think of it as a safety barrier.

---

# Handling Actions That Aren't Simply Allow/Deny

Not every sensitive action should be a flat permission toggle. Some need dynamic, contextual rules:

- **Confirmation-gated permissions** — a "send email" tool might be technically available, but require an explicit user confirmation step before it actually executes, rather than firing immediately on the model's decision.
- **Scoped-by-context permissions** — an agent might be allowed to view _its own_ user's data but not another user's, enforced by the application passing a scoped session token, not by the model "deciding" whose data is whose.
- **Audit logging** — every sandboxed tool call, allowed or blocked, should be logged, so unusual patterns (repeated blocked attempts) can be reviewed later — this is often how injection attempts get noticed in the first place.

```python
def call_tool(agent_session, tool_name, args):
    if tool_name not in agent_session.allowed_tools:
        log_blocked_attempt(agent_session, tool_name, args)
        raise PermissionError(f"{tool_name} not permitted for this agent")

    if tool_name in CONFIRMATION_REQUIRED:
        if not user_confirmed(agent_session, tool_name, args):
            return request_confirmation(agent_session, tool_name, args)

    log_tool_call(agent_session, tool_name, args)
    return execute(tool_name, args)
```

---

# Prompt Sandboxing vs Defensive Prompting

|Defensive Prompting|Prompt Sandboxing|
|---|---|
|Guides AI behavior|Restricts AI capabilities|
|Uses instructions|Uses system permissions|
|Prevents bad decisions|Limits the impact of bad decisions|

Think of it like this.

Defensive Prompting says:

> **"Don't open this door."**

Sandboxing says:

> **"The door is locked."**

---

# Best Practices

Give the AI only the permissions it needs.

Good

```text
Weather Agent

↓

Weather Tool Only
```

Bad

```text
Weather Agent

↓

Weather Tool

Database

Email

Payments

Admin Access
```

Follow the **Principle of Least Privilege**.

Also:

- Distinguish capability sandboxing (which tools/scopes are available) from execution sandboxing (isolating code that actually runs).
- Gate irreversible or sensitive actions behind explicit user confirmation, not just a tool being "available."
- Log every sandboxed decision — allowed and blocked — for auditing and anomaly detection.

---

# Common Beginner Mistakes

### Mistake 1

Giving every AI Agent access to every tool.

Different agents should have different permissions.

---

### Mistake 2

Trusting prompts instead of permissions.

Permissions should always be enforced by the application.

---

### Mistake 3

Allowing sensitive actions without approval.

Critical actions should require:

- User confirmation
- Authentication
- Authorization

---

### Mistake 4

Only restricting which tools are visible, while letting any code the AI generates run with full system access.

If an agent can execute code, that code needs its own execution sandbox (container/VM isolation) — capability restriction alone doesn't cover this.

---

### Mistake 5

Treating permissions as static and global instead of scoped to context.

A support agent should only access the current user's data, not any user's data — this needs to be enforced by scoped tokens/sessions, not left to the model's judgment.

---

# Interview Tip ⭐

A common interview question is:

> **What is Prompt Sandboxing?**

A good answer is:

Prompt Sandboxing limits what an AI Agent can access or do, reducing the impact of Prompt Injection, Jailbreaks, or model mistakes. It covers both capability sandboxing (restricting which tools/permissions are available) and execution sandboxing (isolating any code the agent actually runs, e.g. in a container).

It is an application-level security mechanism, not just a prompting technique.

---

# Where is this Used?

- ChatGPT
- Claude
- Gemini
- LangChain
- LangGraph
- OpenAI Agents SDK
- Google ADK
- Enterprise AI Agents

---

# Key Takeaways

- Prompt Sandboxing limits an AI Agent's permissions.
- There are two kinds: capability sandboxing (tool/permission scope) and execution sandboxing (isolating code that runs).
- Sensitive actions often need confirmation gating and context-scoped permissions, not just a flat allow/deny.
- Logging every sandboxed decision supports auditing and catching attacks.
- It reduces the impact of attacks and mistakes.
- It follows the Principle of Least Privilege.
- Permissions should always be enforced by the application.
- Sandboxing is a core security practice for production AI systems.

---

