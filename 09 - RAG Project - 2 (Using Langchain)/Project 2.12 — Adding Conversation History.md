

## Introduction

So far,

our RAG application answers every question independently.

Example

User:

```text
What is the leave policy?
```

↓

AI

```text
Employees receive 24 paid leave days every year.
```

Now the user asks:

```text
Can they carry it forward?
```

Will the AI understand?

No.

Why?

Because the second question doesn't mention:

```text
Leave Policy
```

The application has forgotten the previous conversation.

This is where **Conversation History** becomes important.

---

# What is Conversation History?

Conversation History stores previous interactions between the user and the AI.

Instead of sending only the current question,

we also send previous messages.

Example

```text
User:
What is the leave policy?

AI:
Employees receive 24 paid leave days.

↓

User:
Can they carry it forward?
```

Now the AI understands:

"They" refers to:

```text
Paid Leave
```

---

# Why Do We Need Conversation History?

Without memory,

every question is treated as a brand-new conversation.

Example

```text
Question 1

↓

Answer

↓

Question 2

↓

Answer
```

No relationship exists between them.

With memory,

the conversation becomes continuous.

---

# Project 1 vs Project 2

Project 1

```text
Question

↓

Answer
```

Project 2

```text
Previous Messages

+

Current Question

↓

Answer
```

---

# What is RunnableWithMessageHistory?

LangChain provides:

```text
RunnableWithMessageHistory
```

Its job is to automatically manage conversation history.

It:

- Stores previous messages
- Sends them with new questions
- Maintains the conversation

---

# Step 1 — Import the Required Classes

```python
from langchain_core.runnables.history import (
    RunnableWithMessageHistory
)

from langchain_community.chat_message_histories import (
    ChatMessageHistory
)
```

---

# Step 2 — Create a Chat History

For learning,

we'll store messages in memory.

```python
message_history = ChatMessageHistory()
```

This object stores:

- User Messages
- AI Messages

---

# Step 3 — Wrap the Existing Chain

Suppose we already have:

```python
chain = build_chain(
    retriever
)
```

Wrap it.

```python
conversation_chain = RunnableWithMessageHistory(

    runnable=chain,

    get_session_history=lambda session_id:

        message_history,

    input_messages_key="question",

    history_messages_key="history"
)
```

Notice something.

We didn't modify our RAG chain.

We simply wrapped it.

---

# Understanding the Parameters

---

## runnable

The original chain.

```python
runnable=chain
```

---

## get_session_history

Returns the chat history for a user session.

For now,

we always return the same history.

Later,

we'll support multiple users.

---

## input_messages_key

Tells LangChain:

```text
Which field contains the user's question?
```

In our case:

```python
question
```

---

## history_messages_key

Tells LangChain:

```text
Where should previous messages be inserted?
```

Our Prompt will later include:

```text
history
```

---

# Step 4 — Update the Prompt

Open:

```text
prompts.py
```

Instead of:

```text
Context

Question
```

We'll also include:

```text
History
```

Example

```python
from langchain_core.prompts import (
    ChatPromptTemplate,
    MessagesPlaceholder
)

RAG_PROMPT = ChatPromptTemplate.from_messages(

    [

        (

            "system",

            """
You are an AI assistant.

Answer using only the provided context.
"""
        ),

        MessagesPlaceholder(

            variable_name="history"
        ),

        (

            "human",

            """
Context:

{context}

Question:

{question}
"""
        )

    ]
)
```

Notice the new line.

```python
MessagesPlaceholder(
    variable_name="history"
)
```

LangChain automatically inserts previous messages here.

---

# Step 5 — Invoke the Chain

Instead of:

```python
chain.invoke(question)
```

we now write:

```python
response = conversation_chain.invoke(

    {

        "question":

        "What is the leave policy?"

    },

    config={

        "configurable":{

            "session_id":"user_1"
        }

    }
)
```

The session ID identifies a conversation.

---

# Step 6 — Ask Another Question

```python
response = conversation_chain.invoke(

    {

        "question":

        "Can they carry it forward?"

    },

    config={

        "configurable":{

            "session_id":"user_1"
        }

    }
)
```

Notice that we didn't repeat:

```text
Leave Policy
```

The chain remembers the previous conversation.

---

# What Happens Internally?

```text
Question

↓

History Retrieved

↓

Prompt Built

↓

LLM

↓

Answer

↓

History Updated
```

The developer doesn't manage history manually.

LangChain does it automatically.

---

# Complete Example

```python
message_history = ChatMessageHistory()

conversation_chain = RunnableWithMessageHistory(

    runnable=chain,

    get_session_history=lambda session_id:

        message_history,

    input_messages_key="question",

    history_messages_key="history"
)
```

---

# Multiple Users

Suppose two users are chatting.

```text
Alice

↓

Conversation A
```

```text
Bob

↓

Conversation B
```

Each user should have independent history.

Example

```python
"user_alice"

"user_bob"
```

Different session IDs create different conversations.

---

# Comparison with Project 1

| Project 1 | Project 2 |
|-----------|-----------|
| No memory | Conversation History |
| Every question independent | Questions connected |
| No previous messages | Previous messages automatically included |
| Manual implementation required | RunnableWithMessageHistory |

---

# Industry Insight

In production systems,

conversation history is rarely stored only in memory.

Instead,

it's stored in databases such as:

- Redis
- PostgreSQL
- MongoDB

This allows users to continue conversations even after refreshing the application or logging in from another device.

---

# Best Practice

Never use one shared history for every user.

Always separate conversations using:

```text
Session ID
```

This prevents users from seeing each other's conversation history.

---

# Debugging Tip

If the model seems to forget previous questions,

verify:

- The session ID remains the same.
- The Prompt includes `MessagesPlaceholder`.
- Conversation history is actually being stored.

Most memory issues come from one of these three problems.

---

# Architecture Review

Our pipeline has evolved.

```text
Question

↓

Conversation History

↓

Retriever

↓

Prompt

↓

LLM

↓

Answer

↓

History Updated
```

The RAG system is now capable of handling multi-turn conversations.

---

# Common Beginner Mistakes

### Mistake 1

Changing the session ID for every request.

A new session ID creates a new conversation.

---

### Mistake 2

Forgetting to add `MessagesPlaceholder` to the Prompt.

Without it,

the history is never sent to the LLM.

---

### Mistake 3

Using one shared history for every user.

Always isolate conversations by session.

---

### Mistake 4

Confusing RAG with Conversation History.

RAG retrieves knowledge from documents.

Conversation History remembers previous messages.

These solve different problems.

---

# Key Takeaways

- Conversation History allows the AI to remember previous messages.
- `RunnableWithMessageHistory` automatically manages memory.
- `MessagesPlaceholder` inserts previous messages into the prompt.
- Session IDs keep user conversations separate.
- RAG and Conversation History work together to build intelligent conversational applications.