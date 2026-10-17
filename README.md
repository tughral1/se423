# SE423 Copilot Kit

GitHub Copilot customization for SE423 Software Construction lab/exam tasks (Java, Labs 1–6: clean code, abstraction/encapsulation, SOLID, creational, structural and behavioral patterns).

## Exam-day setup (lab PC)

```powershell
git clone https://github.com/tughral1/se423.git SE423_Exam
cd SE423_Exam
code .
```

Then in VS Code:

1. Click **Yes, I trust the authors** if prompted.
2. Make sure Copilot is signed in (icon at bottom right).
3. Open Copilot Chat (Ctrl+Alt+I) and send: `What answer format are you required to use for lab tasks?`
   Expand the **References** line of the reply; `copilot-instructions.md` must be listed.
4. Pick an agent from the mode dropdown: `pattern-expert`, `solid-refactorer`, or `lab-task-solver`.
   Or type `/` for `/pick-pattern`, `/implement-pattern`, `/refactor-solid`, `/compare-patterns`, `/verify-solution`.
5. Write the exam code in `Main.java` inside this folder.

If the agents do not appear in the dropdown (older Copilot): rename `.github/agents/*.agent.md` to `*.chatmode.md`, move them to `.github/chatmodes/`, then Ctrl+Shift+P → Reload Window.

If nothing loads at all: open `.github/copilot-instructions.md`, copy all of it, paste as the first chat message followed by "Follow these instructions for this whole session."

## What is here

| Path | Purpose |
|---|---|
| `.github/copilot-instructions.md` | Auto-loaded rules: answer format, Java style, pattern cue table, SOLID detection table |
| `.github/agents/` | Three custom agents for the chat mode dropdown |
| `.github/prompts/` | Five slash prompts |
| `.github/instructions/java.instructions.md` | Rules applied to every `.java` file |
| `.vscode/settings.json` | Turns on instruction files, prompt files and AGENTS.md |
| `AGENTS.md` | Portable summary for any AI agent |
