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


## Tips and tricks

- [Reflective lessons](reference/scenario-reflective-lessons.md): Tell the agent to reflect on the lesson learned after a correction.

