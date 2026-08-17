## Knowledgebase

@knowledgebase/INDEX.md

This project keeps a committed knowledgebase in `knowledgebase/`. The procedure lives in the `knowledgebase` skill; its [design notes](.claude/skills/knowledgebase/references/design.md) explain why the system is built this way.

**Checkpoint — before ending any turn** that made a decision, added/changed a feature or subsystem, changed infrastructure, discovered an issue (solved or not), or invalidated a recorded assumption: invoke the `knowledgebase` skill to capture and reconcile it. Report entries written, especially edits outside the current task.

Skip turns that changed nothing worth recording (see the skill's capture bar).
