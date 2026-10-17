---
mode: agent
description: Diagnose and refactor poorly designed Java using clean-code, cohesion/coupling and all five SOLID principles, with before/after comparison.
---

Code or task statement:
${input:code:Paste the poorly designed code, or the task statement if you must write the poor version first}

Act as `solid-refactorer`.

1. Diagnosis table: symptom (quoted) → concept violated → lab. Check SRP, OCP, LSP, ISP, DIP, encapsulation, information hiding, cohesion, coupling, stable interfaces, duplication, hard-coding, giant methods.
2. Class map of the refactored design (abstractions first).
3. Refactored code, one file, one `public class Main`, constructor injection everywhere, demo covering every requirement, expected output.
4. Proof of OCP: add one new implementation without touching the high-level service.
5. Explanation per principle in the shape *Where violated / What changed / How it improves*, then a Before vs After comparison table and a list of classes added / split / moved.

If no poor version was supplied and the task asks for one, write the deliberately poor initial version first and label it.
