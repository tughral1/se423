# AGENTS.md — SE423 exam workspace

This workspace is for SE423 Software Construction lab/exam tasks in Java. Any AI agent working here must read and follow `.github/copilot-instructions.md` (answer format, Java style, pattern recognition cues, SOLID detection table) and `.github/instructions/java.instructions.md` (code rules).

Specialised agents are defined in `.github/agents/`:

- `pattern-expert` — choose and implement a design pattern from a scenario.
- `solid-refactorer` — diagnose and refactor poorly designed code with SOLID and Lab 1–2 principles.
- `lab-task-solver` — turn a full task statement into the complete deliverable and self-verify it.

Reusable prompts in `.github/prompts/`: `/pick-pattern`, `/implement-pattern`, `/refactor-solid`, `/compare-patterns`, `/verify-solution`.

Non-negotiables: one file, one `public class Main`, interfaces + constructor injection, no type-string if/else outside a factory, never modify a class the task calls "existing", always include the explanation section (what was wrong, what was applied, how it improved) and the expected console output.
