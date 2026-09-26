---
date: 2026-09-26
authors:
  - ayushpatel
categories:
  - AI
  - Optimization
  - Tools
description: A quick guide to installing Laya, making your first prediction, and understanding where it can be useful.
---

# Getting Started with Laya

## Why Laya?

Most AI applications use large language models when they need to understand text. But sometimes you don't need a model to **generate an answer** — you just need it to make a decision.

[Laya](https://huggingface.co/convaiinnovations/laya) is a non-autoregressive **System 1 decision model** designed for fast, structured decisions. Instead of generating text, you provide some input and ask typed questions such as:

- Which category does this belong to?
- How urgent is this?
- Is this a valid request?
- Which option should be selected?
- What is the probability of a certain condition?

Laya can return these decisions in a single forward pass and supports 100+ languages.

This makes it interesting for applications where **speed, predictable output, and structured decisions** matter more than generating natural-language responses.

## Installation

Laya requires Python 3.10 or newer.

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it:

**Windows:**

```powershell
.\.venv\Scripts\activate
```

**Linux/macOS:**

```bash
source .venv/bin/activate
```

Then install Laya:

```bash
python -m pip install laya
```

You can verify the installation with:

```bash
python -c "import laya; print(laya.__version__)"
```

The model weights are downloaded from Hugging Face when you first run inference.

## Your First Laya Prediction

The easiest way to get started is with the `Router`.

```python
from laya import Router

router = Router()

state = {
    "subject": "Duplicate charge",
    "body": "I was charged twice for my subscription. Please refund one of the charges."
}

questions = {
    "department": {
        "type": "choice",
        "instructions": "Which department should handle this request?",
        "criteria": {
            "billing": "invoices, payments and refunds",
            "technical": "bugs and system problems",
            "sales": "pricing and new contracts"
        }
    },
    "urgent": {
        "type": "noul",
        "instructions": "Is this request urgent?"
    }
}

result = router.predict(state, questions)

print(result["answers"])
```

The important idea is that you define **what decision you want**, rather than asking the model to generate a response.

For example, the output could contain a structured decision such as:

```text
department → billing
urgent → true
```

This makes the result easier to pass directly into another program.

## What Can You Use Laya For?

Laya is particularly interesting for **high-volume, structured decision-making**.

### 1. Ticket Routing

Automatically classify incoming support tickets.

```text
Customer message
       ↓
     Laya
       ↓
Billing / Technical / Sales
       ↓
Correct workflow
```

### 2. Spam and Content Filtering

Determine whether an email, message, or document meets certain criteria.

For example:

```text
Is this spam?
Is this relevant?
Does it contain a complaint?
```

### 3. Risk Scoring

You can ask Laya to produce structured scores or probabilities rather than natural-language explanations.

This could be useful for:

- Customer churn signals
- Document screening
- Lead qualification
- Priority detection
- Operational alerts

### 4. Fast Decisions in Software Systems

Because Laya is designed around a single forward pass rather than generating long responses, it can be useful when an application needs many small AI decisions.

For example:

```text
Sensor / Event / Message
          ↓
        Laya
          ↓
   Structured decision
          ↓
     Application
```

This could be useful in automation, monitoring, robotics, or other systems where the AI output needs to feed directly into software logic.

## Laya vs. an LLM

Laya isn't meant to replace models such as ChatGPT or other generative LLMs.

The difference is roughly:

| Task                         | Laya              | Generative LLM                |
| ---------------------------- | ----------------- | ----------------------------- |
| Classification               | ✓                 | ✓                             |
| Structured decisions         | ✓                 | ✓                             |
| Probability/score            | ✓                 | Possible                      |
| Text generation              | ✗                 | ✓                             |
| Summarization                | ✗                 | ✓                             |
| Creative writing             | ✗                 | ✓                             |
| Conversational assistant     | Limited           | ✓                             |
| Very fast repeated decisions | Designed for this | Usually more expensive/slower |

A useful way to think about it is:

> **LLMs generate. Laya decides.**

## Limitations

Laya is specialized, so there are a few important limitations.

### It doesn't generate text

If your application needs an explanation, email, summary, or conversational response, you'll need a generative model instead.

### Accuracy still depends on your task

A fast model isn't automatically a reliable model. You should test Laya against your own data before putting it into a production workflow.

This is especially important for unusual domains or decisions that have significant consequences.

### Long documents need extra consideration

The multilingual model supports longer inputs with an increased context limit, but the project documentation reports that accuracy becomes less stable as documents get very long.

For long documents, test performance on your actual data rather than assuming that increasing the context window will maintain accuracy.

### Model downloads can be significant

The model checkpoints are hundreds of megabytes, so the first setup can take some time and disk space.

## Useful Extras

Laya also provides optional integrations depending on how you want to deploy it:

```bash
pip install "laya[serve]"
```

for a self-hosted HTTP server, or:

```bash
pip install "laya[langchain]"
```

for LangChain/LangGraph integration.

There are also optional packages for ONNX, MCP, and GPU acceleration.

## When Should You Try Laya?

Laya is worth experimenting with when your problem looks like:

> **Input → Decision → Action**

rather than:

> **Input → Generated text**

For example:

```text
Email
  ↓
Laya
  ↓
Classify + score
  ↓
Business logic
  ↓
Action
```

That makes it an interesting tool for developers building **automation, data pipelines, AI agents, monitoring systems, and other applications where AI needs to make lots of small, structured decisions.**

## Conclusion

Laya takes a different approach to AI than traditional generative models. Instead of trying to generate a response, it focuses on making fast, structured decisions.

If your application only needs an AI model to **classify, score, route, or evaluate something**, Laya can be a useful tool to experiment with.

For more complex tasks involving reasoning, explanations, or text generation, a traditional generative LLM is likely a better fit.

**Useful resources:**

- [Laya on Hugging Face](https://huggingface.co/convaiinnovations/laya)
- [Laya on GitHub](https://github.com/NandhaKishorM/laya)
- [Laya on PyPI](https://pypi.org/project/laya/)
