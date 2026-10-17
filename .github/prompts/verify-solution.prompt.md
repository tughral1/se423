---
mode: agent
description: Audit a finished SE423 solution against the task statement and the grading rubric before submission.
---

Task statement:
${input:task:Paste the task statement}

Solution to audit: the currently open file (or paste below).
${input:solution:Optional, paste the solution if not open}

Act as `lab-task-solver` in verification mode only. Do not rewrite the solution unless something is wrong.

1. Build the requirement checklist from the task statement and tick each item with the exact line of code that satisfies it. Mark missing items clearly.
2. Confirm the pattern/principle actually implemented matches the one claimed, using the three questions (what is in the hole, who swaps it, how many).
3. Check the "do not modify X" constraints are respected.
4. Check the structural details: `final` template method, terminal State does not transition, Observer removal works, Builder constructor private, Factory throws on unknown type, Proxy lazy + auth, Decorator stacks, DIP via constructor injection, no type-string if/else outside a factory.
5. Trace the program output by hand and compare with the stated expected output; report any mismatch.
6. Check the explanation section covers: what was wrong, what was applied and where, how it improved (CLO-2).

Report as a table: Requirement → Status (OK / Missing / Wrong) → Evidence or fix.
