---
mode: agent
description: Decide which design pattern (or SOLID principle) fits a scenario, with cue, rejected alternatives, role mapping and skeleton.
---

Scenario: ${input:scenario:Paste the task scenario here}

Act as `pattern-expert`. Using the recognition-cue table in `.github/copilot-instructions.md`:

1. Name the single best pattern and its category.
2. Quote the exact phrase in the scenario that triggered it.
3. Rule out the two most confusable alternatives with one question each (e.g. "Does the plugged-in object replace itself? No → not State").
4. Map pattern roles onto class names drawn from the scenario.
5. Give the minimal Java skeleton (interfaces and class signatures only, no bodies).

If the scenario is really a SOLID/refactoring problem rather than a pattern problem, say so and switch to the SOLID table instead.
