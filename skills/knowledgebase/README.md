# Knowledgebase

A skill to manage a projects knowledgebase.

## Installation in a project

1. **The skill itself**: Copy paste it into your project as your model requires.
2. **The design.md file**: Supporting document elaborating on the skills usage - Copy paste it into your project as your model requires.
3. **AGENT/CLAUDE.md instructions**: Add its usage to the *AGENT/CLAUDE.md* file so it can better be used each round.

## Modes

There are 4 modes:

* **init**: Called the first time for a project. Scans the project files for documentable things.
    Try running it like this:
    ```
    /knowledgebase init. Ask me questions for any mentions of a decision or similar or anything that sounds like its missing some context or a piece.
    ```
* **continuous**: Called continuously for each round-end with the agent, catches new knowledge or updated knowlegde and decisions. - called automatically.
* **review**: Verify the quality and truthfulness of the knowledgebase. Good to run from time-to-time.
    ```
    /knowledgebase review all documents
    ```
* **migrate-memory**: Memories about decisions or design choises etc that is VERY important for the whole project to store might have been stored in you personal agent memory. Thos mode moves them into the project isntead. Memories are reserved for personal preferences. **run this after init for a new project**

På ett befintligt projekt börjar du med init-läget, som skannar repot och lägger upp strukturen med det som redan går att belägga i källan. Har du projektkunskap liggande i personligt minne flyttar migrate-memory över den — den frågar innan något raderas.

## File structure

```
knowledgebase/
├── INDEX.md          scannable summary of the knowledgebase
├── decisions/        decision + motivation (append-only)
├── architecture/     how hard-to-find parts/features/flows etc works
├── issues/           issues (open / solved / workaround)
└── shared/           crossing premisses — one premiss, one place, referenced by other documents
```
