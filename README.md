# ai-cookbook
A place to gather and collaborate on best practices and skills and instructions of software development with Agentic tools

## Custom output styles

For instructions to the agent to respond to *you* in certain ways or styles in the chat, use a custom `output-style` instead of adding your instruction to you `CLAUDE.md` for example.
`CLAUDE.md` is more suited for files and `output-style` is better suited for how it talks to *you*.

Create one under `~/.claude/output-styles/` as a `.md` file and set it in `settings.json` `"outputStyle": "name-of-your-style"`.
See [communication.md](output-styles/communication.md) as an example.


## Skills

| Name | Description |
| - | - |
| [interview](skills/interview/SKILL.md) | Have the agent interview you to help you shape your own idea. May also help you maintain Comprehension |
| [knowledgebase](skills/knowledgebase/SKILL.md) **outdated** | Manage a knowledge base for the project it is installed in |


## Practices

- [Decisions documentation](reference/decisions-documentation.md): Have the agent write down every decision and its reasoning as a file, and keep them current. Settled questions stay settled across sessions.
- [Project phases](reference/project-phases/project-phases.md): Tell the agent which phase the project is in and what each phase allows. It takes the right amount of risk for where the project actually is.
- [Definition refinement](reference/definition-refinement.md): Write your idea for something non-trivial into an intent file in the repo, have the agent interview you about it to fill gaps and catch contradictions, then ask it to "update the intent file as if I had written it from the start". The agent builds what you meant instead of making decisions that look random.


## Tips and tricks

- [Reflective lessons](reference/scenario-reflective-lessons.md): Tell the agent to reflect on the lesson learned after a correction. Catches misunderstandings early.
