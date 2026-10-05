# Learn Jev

A short, hands-on introduction to TypeSafe AI's **Jev** model: send context as `state`, ask typed questions, and use the structured answers in code.

## Start here

- [Guide](docs/guide.md) — the mental model, question types, API shape, and how to read answers.
- [Experiments](docs/experiments.md) — requests we ran, their observed responses, and a task-routing comparison.
- [HTML presentation](presentation/index.html) — a standalone slide deck. Download or clone the repository and open the file in a browser; use the arrow keys to navigate.
- [Example requests](examples/) — JSON bodies you can adapt for the [TypeSafe Playground](https://console.typesafe.ai/playground) or API.

## The idea in one minute

```text
state (text or JSON) + named typed questions
                    ↓
              Jev / System One
                    ↓
answers under the same names: probabilities, choices, or scores
```

Jev's three question types are **Noul** (probability that a yes/no answer is yes), **Choice** (one option from a defined set), and **Score** (a position on ordered levels). Several questions can share one state in a single request. The questions are evaluated independently, so each should ask one clear thing. [TypeSafe's introduction](https://docs.typesafe.ai/introduction) explains the model and its primitives.

## Try it

1. Sign in to the [Playground](https://console.typesafe.ai/playground). Access may depend on TypeSafe's early-access rollout.
2. Paste a state, such as `Split all my active winodws`.
3. Add a Noul question: `Does this instruction ask for an action to be performed in the user's local desktop environment?`
4. Read the value under `answers`: it is the model's estimated probability of **yes**. Our recorded run returned `0.95`; another run may differ.

For an API call, see TypeSafe's [quick start](https://docs.typesafe.ai/introduction/quickstart) and the [request schema](https://docs.typesafe.ai/api). Keep API keys outside this repository and out of request states.

## Sources and scope

These notes summarize our learning session on **October 5, 2026** and TypeSafe's [launch article](https://typesafe.ai/blog/introducing-system-one-models-and-jev), [API reference](https://docs.typesafe.ai/api), and [question guide](https://docs.typesafe.ai/primitives). The recorded outputs are observations from four example calls, not benchmarks or guarantees. TypeSafe's performance and calibration claims should be tested on your own workload.

Assisted-by: Codex
