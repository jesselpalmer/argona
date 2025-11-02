# Argona

The Agent-Oriented Language for AI-Native Systems

## Introduction

Argona is a programming language and runtime for building **safe, observable, and cost-aware AI agents**. The name Argona comes from **Ag**ent-**O**rie**n**ted Language, reflecting a design where goals, tools, memory, and reasoning are first-class language constructs.

## Language Overview

| Construct | Purpose |
|---------|-------------|
| `goal` | Declarative intents describing *what* to achieve |
| `plan` | Procedural strategies describing *how* to achieve it |
| `actor` | Concurrency units that execute plans under a policy |
| `tool` | Typed, sandboxed capabilities an agent can call (e.g., APIs, memory, I/O) |
| `memory` | Built-in key-value, vector, and graph stores for recall and context |
| `policy` | Budgets, safety filters, and approval rules controlling agent behavior |
| `aon` | *Argona Object Notation* - a JSON-compatible data format that lets agents express uncertainty, cite their sources, and include rich data such as embeddings or images |
| `think` | Model invocation block with AON schema and prompt |
| `observe / effect` | Explicit side-effects tracked by runtime |

### Example

```ts
// ------------------------------------------------------
// Argona Example - Writing a Haiku
// ------------------------------------------------------

// Define a global constant for haiku syllable count.
const HAIKU_SYLLABLES = 17

// Define a goal describing what the agent should achieve.
// Goals declare success conditions, not execution details.
goal WriteHaiku(topic: string) -> { text: string } {
  success when len(text) >= HAIKU_SYLLABLES
}

// Define a plan describing how to achieve the goal.
// Plans can call models, use tools, write to memory, or perform effects.
plan HaikuPlan(input: { topic: string }) -> { text: string } {
  // Call a reasoning model and request structured output in AON.
  // AON (Argona Object Notation) is a JSON-compatible format that lets
  // agents represent uncertainty, cite sources, and include rich data.
  let poem = think model('gpt-4o-mini') aon {
    schema { line1: string, line2: string, line3: string }
  }
  """
  Write a haiku (5/7/5) about ${input.topic}.
  """

  // Return structured output.
  return { text: poem }
}

// Define an entrypoint actor that executes the plan.
actor Main {
  on start {
    let result = run WriteHaiku('first snowfall') using HaikuPlan
    let poem = result.text

    // Handle output
    effect Console({
      text: 'Haiku about first snowfall:\n' +
        poem.line1 + '\n' +
        poem.line2 + '\n' +
        poem.line3
    })
  }
}
```

#### Example CLI usage

```bash
argona run examples/haiku.argona
```

#### Example Output

```bash
Haiku about first snowfall:
Soft flakes touch the ground
Whispers fade beneath the cold
The world holds its breath
```
