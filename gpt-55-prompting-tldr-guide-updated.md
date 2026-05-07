# GPT-5.5 Prompting TLDR Guide

## Core idea

Prompt GPT-5.5 by defining the **outcome, constraints, success criteria, and output shape**. Avoid over-prescribing every internal step unless the workflow genuinely requires it.

This works across **API, ChatGPT.com, and Codex**, but some controls are platform-specific.

---

## Universal prompting pattern

Use this everywhere:

```text
Goal:
[What should be accomplished]

Context:
[Relevant background, files, repo state, user intent]

Success criteria:
- [What must be true when done]
- [Evidence, tests, citations, or checks required]
- [What should be avoided]

Constraints:
- Do not invent facts.
- Ask only if blocked by missing information.
- Keep the output concise unless depth is requested.

Output:
[Bullets / table / JSON / code diff / report / PR summary]
```

---

## What works where

| Prompting / control | API | ChatGPT.com | Codex |
|---|---:|---:|---:|
| Outcome-first prompting | Yes | Yes | Yes |
| Success criteria | Yes | Yes | Yes |
| Clear constraints | Yes | Yes | Yes |
| Exact output format | Yes | Yes | Yes |
| Preambles / progress updates | Yes | Yes | Yes |
| `reasoning.effort` | API parameter | Thinking-time toggle / model picker | Model/task setting |
| `text.verbosity` | API parameter | Prompt it manually | Prompt it manually |
| Structured Outputs / JSON schema | Native API feature | Prompt-only approximation | Depends on environment/tools |
| `previous_response_id` / state handling | API-only | Managed by ChatGPT UI | Managed by Codex environment |
| Repo-aware coding workflows | Via tools/API | Limited unless files are provided | Native strength |

---

## API usage guidance

Use API-specific controls when building products:

| Need | Use |
|---|---|
| Faster / cheaper responses | Lower `reasoning.effort` |
| Hard reasoning / coding / agentic tasks | Higher `reasoning.effort` |
| Short answers | `text.verbosity: "low"` |
| Long explanations / refactors | `text.verbosity: "medium"` or `"high"` |
| Reliable JSON | Structured Outputs |
| Multi-turn app state | Responses API + `previous_response_id` |
| Reused system prompts | Put stable instructions first for prompt caching |

API example:

```json
{
  "model": "gpt-5.5",
  "reasoning": { "effort": "medium" },
  "text": { "verbosity": "low" },
  "input": "Summarize this incident report into owner, impact, root cause, and next actions."
}
```

---

## ChatGPT.com usage guidance

You usually cannot set API params directly, so write the behavior into the prompt:

```text
Answer concisely.
Use a table.
Do not invent facts.
Ask only if missing information blocks the answer.
Give a TLDR at the bottom.
```

For harder tasks, use **Thinking** or **Pro** when available. GPT-5.5 Thinking may show a short preamble before working, and ChatGPT can support tools like web search, data analysis, file analysis, image analysis, Canvas, image generation, Memory, and Custom Instructions depending on the selected model/plan.

---

## Codex usage guidance

Codex benefits most from **task + success criteria + constraints**.

```text
Goal:
Fix the bug causing checkout retries to duplicate charges.

Success criteria:
- Identify the root cause.
- Make the smallest safe code change.
- Add or update tests.
- Run relevant checks if available.
- Summarize changed files and trade-offs.

Constraints:
- Do not refactor unrelated code.
- Do not change public APIs unless necessary.
- Preserve existing logging and metrics.
```

For Codex, avoid vague prompts like “clean this up.” Give it the repo objective, guardrails, test expectations, and what a good PR should contain.

---

## Do / avoid

| Do | Avoid |
|---|---|
| Define “done” clearly | Long procedural scripts |
| Give constraints and priorities | Contradictory instructions |
| Specify output format | Vague “make it better” prompts |
| Use tests/evidence where relevant | Asking for hidden chain-of-thought |
| Tune reasoning/verbosity in API | Treating API knobs as ChatGPT prompts |

---

## TLDR

The GPT-5.5 prompting style is **not API-only**. The main pattern works in **API, ChatGPT.com, and Codex**: define the goal, context, success criteria, constraints, and output format. API adds extra programmable controls like `reasoning.effort`, `text.verbosity`, Structured Outputs, and `previous_response_id`; ChatGPT and Codex expose similar behavior through UI/model settings and natural-language instructions.
