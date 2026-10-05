# Jev: concepts and API

## Mental model

Jev is a TypeSafe **System One** model for fast, structured decisions. A request supplies a `state` (the evidence or application context) and a map of `questions` (the judgments to make). Jev returns one answer per question under the same ID. It does not generate a free-form write-up; your code uses the typed answers to decide what happens next. [Introduction](https://docs.typesafe.ai/introduction) · [API reference](https://docs.typesafe.ai/api)

```json
{
  "state": "My order arrived damaged. Can I get my money back today?",
  "model": "jev-latest",
  "questions": {
    "refund_requested": {
      "type": "noul",
      "instructions": "Is the customer asking for a refund?"
    }
  }
}
```

`state` can be a string, object, or array. Each question has a `type` and `instructions`; Choice and Score also require `criteria`. The question ID (`refund_requested` above) is for your code: the model's judgment comes from `instructions` and `state`, not the ID. The REST endpoint is `POST https://api.typesafe.ai/v1/systemone` with a bearer API key. [API reference](https://docs.typesafe.ai/api)

## Three question types

| Type | Ask | Extra request field | Main answer fields |
| --- | --- | --- | --- |
| **Noul** | Is this true? | Optional `criteria.true` and `criteria.false` | `noul` from 0 to 1 |
| **Choice** | Which option? | `criteria`: option names and descriptions | `choice`, `probabilities`, `confidence` |
| **Score** | Which level on a spectrum? | `criteria`: ordered level descriptions | `score`, `legend`, `probabilities`, `confidence` |

A Noul value of `0.93` means Jev assigns a 93% probability to **yes given the supplied state**. It does not mean the subject has 93% of a trait. A value near `0.5` means yes and no have similar probability. Noul has no separate `confidence` field. [Noul guide](https://docs.typesafe.ai/primitives/noul)

Choice returns only options you supplied, with a probability for each. Score returns a probability-weighted position across your ordered levels; it can fall between levels. Choice and Score add `confidence`, which summarizes how concentrated their probability distribution is. [Choice guide](https://docs.typesafe.ai/primitives/choice) · [Score guide](https://docs.typesafe.ai/primitives/score)

## Ask several narrow questions together

Every question in one request sees the same state and is evaluated independently. For a support message, ask `refund_requested` and `urgent` as separate Nouls. Combining them into “Is the customer urgent and asking for a refund?” makes one probability harder to interpret. TypeSafe recommends one judgment per question and composition in code. [Primitives](https://docs.typesafe.ai/primitives) · [Noul guide](https://docs.typesafe.ai/primitives/noul)

```text
state: "My order arrived damaged. Can I get my money back today?"
  ├─ refund_requested? → 0.98  (observed)
  └─ urgent?           → 0.92  (observed)
```

The same pattern works for task routing: assess complexity, need for tools, and need for more context separately; then choose a handler in code. TypeSafe describes this as [intent routing](https://docs.typesafe.ai/patterns/intent-routing).

## Read results responsibly

- A typed response constrains **format and possible options**. It does not prove the judgment is correct.
- Probabilities are model estimates. Test them on your own examples before choosing thresholds for automatic actions.
- If a task needs current facts, tools, or extended reasoning, route it to the appropriate workflow. Jev's role can be to classify the request; it need not complete the entire task.
- Keep credentials out of `state`, `instructions`, examples, and committed files.

TypeSafe's [launch article](https://typesafe.ai/blog/introducing-system-one-models-and-jev) makes performance and reliability claims. Treat those as vendor claims until you reproduce them for your workload.

Assisted-by: Codex
