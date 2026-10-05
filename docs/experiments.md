# Experiments from our learning session

These are the request bodies and responses observed on **October 5, 2026** with `jev-latest`, which resolved to `jev-1.13.0` for these calls. Results may vary across runs or model versions. The JSON files in [`examples/`](../examples/) contain the request bodies without credentials.

## 1. Is this a local desktop action?

We supplied the original text, including its typo:

```json
{
  "state": "Split all my active winodws",
  "model": "jev-latest",
  "questions": {
    "is_local_command": {
      "type": "noul",
      "instructions": "Does this instruction ask for an action to be performed in the user's local desktop environment?"
    }
  }
}
```

Jev returned `answers.is_local_command.noul = 0.95`. That is a probability about **the meaning of the supplied text**. The API call did not arrange any windows.

## 2. Two judgments about one message

```json
{
  "state": "My order arrived damaged. Can I get my money back today?",
  "model": "jev-latest",
  "questions": {
    "refund_requested": {
      "type": "noul",
      "instructions": "Is the customer asking for a refund?"
    },
    "urgent": {
      "type": "noul",
      "instructions": "Does the customer express urgency?"
    }
  }
}
```

The observed response had `refund_requested = 0.98` and `urgent = 0.92`. Each number answers its own question against the same message. TypeSafe recommends this structure for independent judgments. [Noul guide](https://docs.typesafe.ai/primitives/noul)

## 3. A harder test: route a task

We sent [`examples/task-routing.json`](../examples/task-routing.json) to Jev. Its task was: “A Python project began failing tests after a dependency upgrade. Inspect the failures and changelog, find the cause, make the smallest safe fix, and verify it with targeted tests.” We then sent the same four questions with a simpler state: “In the current repository, list the Markdown files containing the exact text jev-latest.”

| Task | Best handler (Choice) | Reasoning complexity (Score, 0–3) | Needs tools (Noul) | Needs more context (Noul) |
| --- | --- | ---: | ---: | ---: |
| Debug tests after a dependency upgrade | `large_model` (1.00) | 2.92 | 0.98 | 0.97 |
| Find Markdown files containing `jev-latest` | `tool_only` (1.00) | 0.33 | 0.97 | 0.89 |

The repository search still needs tools and access to the files, which explains its high scores for the last two questions. Those scores alone do not imply it needs a large model. Jev classified the task; it did not inspect the repository or fix any tests. These are observed outputs from one run of `jev-1.13.0`, not validated routing thresholds.

The `best_handler` Choice gives a suggested route; the other questions expose useful factors behind that route. A real router should test these answers against labeled tasks and set its own thresholds. See TypeSafe's [intent-routing pattern](https://docs.typesafe.ai/patterns/intent-routing) and [confidence guidance](https://docs.typesafe.ai/confidence).

## Reproduce

Open the [TypeSafe Playground](https://console.typesafe.ai/playground) and enter a state and the corresponding questions. For the HTTP API, follow TypeSafe's [quick start](https://docs.typesafe.ai/introduction/quickstart). The request bodies in `examples/` contain no API key; keep your key outside the repository.

Assisted-by: Codex
