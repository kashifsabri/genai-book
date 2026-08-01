# 10. RNN & LSTM Overview

## Introduction

In the previous chapter, we learned that **Word2Vec** creates **static embeddings**.

This means every word always receives the same vector, regardless of the sentence.

For example, the word **bank** has different meanings in these sentences:

```text
I deposited money in the bank.
```

```text
The boat stopped near the river bank.
```

Word2Vec cannot distinguish between these meanings because it assigns the same embedding to the word **bank**.

Researchers needed a better solution.

They wanted a model that could understand **context**.

The first successful attempt was the **Recurrent Neural Network (RNN)**.

Later, RNNs were improved by **Long Short-Term Memory (LSTM)** networks.

---

## 10.1 What is an RNN?

An **RNN (Recurrent Neural Network)** is a type of neural network designed to process **sequential data**.

Instead of looking at one word at a time, an RNN processes words **one after another**.

For example:

```text
I

↓

love

↓

Machine

↓

Learning
```

Each word is processed in sequence.

Unlike traditional neural networks, an RNN remembers some information from the previous words while processing the next word.

---

## 10.2 Why Were RNNs Introduced?

Traditional neural networks treat every input independently.

For text, this is a problem because the meaning of a word often depends on the words that came before it.

Consider this sentence:

```text
The cat is sleeping.
```

When reading the word:

```text
sleeping
```

it helps to know that the earlier word was:

```text
cat
```

RNNs were introduced to remember previous words while processing the sentence.

---

## 10.3 How Does an RNN Work?

An RNN processes one word at a time.

It also carries information from the previous step.

```text
Word 1
   │
   ▼
Hidden State
   │
   ▼
Word 2
   │
   ▼
Hidden State
   │
   ▼
Word 3
```

The **hidden state** acts like a small memory.

It carries useful information from earlier words to later words.

---

## 10.4 The Limitation of RNNs

Although RNNs introduced memory, that memory was limited.

As sentences became longer, the model gradually forgot earlier words.

For example:

```text
The boy who was wearing a blue jacket and carrying a large backpack walked into the classroom because...
```

By the time the model reaches the end of the sentence, it may no longer remember important information from the beginning.

This makes it difficult for RNNs to understand long sentences.

---

## 10.5 What is an LSTM?

To solve the memory problem of RNNs, researchers developed **LSTM (Long Short-Term Memory)** networks.

An LSTM is a special type of RNN.

It is designed to remember important information for a much longer time.

Instead of forgetting earlier words quickly, an LSTM learns what information should be:

- Remembered
- Updated
- Forgotten

This allows it to handle longer sequences more effectively than a standard RNN.

---

## 10.6 RNN vs LSTM

| RNN | LSTM |
|-----|------|
| Processes sequential data | Processes sequential data |
| Has limited memory | Has longer memory |
| Struggles with long sentences | Handles long sentences better |
| Simpler architecture | More complex architecture |

LSTMs were a significant improvement over traditional RNNs.

---

## 10.7 Python Example

PyTorch provides built-in classes for both RNNs and LSTMs.

### Creating an RNN

```python
import torch.nn as nn

rnn = nn.RNN(
    input_size=10,
    hidden_size=20,
    batch_first=True
)

print(rnn)
```

Output

```text
RNN(10, 20, batch_first=True)
```

---

### Creating an LSTM

```python
import torch.nn as nn

lstm = nn.LSTM(
    input_size=10,
    hidden_size=20,
    batch_first=True
)

print(lstm)
```

Output

```text
LSTM(10, 20, batch_first=True)
```

You don't need to understand every parameter right now.

We'll study PyTorch sequence models in more detail later.

---

## 10.8 Why RNNs and LSTMs Were Still Not Enough

Although LSTMs improved memory, they still had several limitations.

- They processed words one by one.
- Training was relatively slow.
- Remembering very long contexts was still difficult.
- Parallel processing was limited.

Researchers wanted a model that could process an entire sentence at once while understanding relationships between all words.

This led to one of the biggest breakthroughs in Artificial Intelligence.

The **Transformer**.

---

## Transformer Connection

The evolution of Natural Language Processing looks like this:

```text
Bag of Words
      │
      ▼
TF-IDF
      │
      ▼
Word2Vec
      │
      ▼
RNN
      │
      ▼
LSTM
      │
      ▼
Transformer
```

Each new approach solved some of the limitations of the previous one.

Transformers became the foundation of modern Large Language Models because they understand context much better than earlier approaches.

---

## Key Takeaways

- RNNs were introduced to process sequential data such as text.
- RNNs remember information from previous words using a hidden state.
- Standard RNNs struggle with long-term dependencies.
- LSTMs improve RNNs by remembering important information for longer.
- RNNs and LSTMs were major milestones in NLP.
- Their limitations eventually led to the development of Transformers.