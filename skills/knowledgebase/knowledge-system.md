# Knowledge system

How this project (and others) keeps a durable, agent-facing knowledgebase, and **why**
it is built this way. The mechanics live in the `knowledgebase` skill
(`.claude/skills/knowledgebase/SKILL.md`); this document is the rationale and the
contract.

## Purpose

Code and git history record *what* changed. They do **not** record *why* a decision was
made, which alternatives were rejected, how a non-obvious subsystem actually works, or
what gotcha cost an afternoon. That knowledge normally lives only in a person's head and
is lost between sessions. The knowledgebase (KB) captures it so **future agentic
sessions start informed** instead of rediscovering it.

## What goes in — the capture bar

Record something only when **both** hold:

1. It is **non-obvious** *or* **significant** — a decision, an interface/behavior
   change, an infrastructure change, an issue, or a hard-won insight; **and**
2. A future agent **could not recover it** from code + git + `CLAUDE.md`/`AGENTS.md`.

**Why the second clause:** without a "not recoverable from the repo" floor, the KB fills
with noise (renames, version bumps, "a function now exists") and its signal drowns. The
floor is what keeps mechanical changes out while letting the *why* and the *gotcha* in.

Rejected approaches are explicitly in scope — git barely shows abandoned work, so a path
tried and dropped is among the highest-value things to record.

## Two verbs, kept separate — capture vs reconcile

The original instinct was "keep the *entire* KB updated." Taken literally that is a trap:
it has no floor (bloat) and no ceiling (an agent can't re-verify a large KB every turn,
so it gives up and updates nothing). The fix is to split one goal into two rules with
different mechanics:

- **Capture** — *append* a new entry when it clears the bar. Bounded by the bar.
- **Reconcile** — when new knowledge **contradicts** an existing entry, fix it. Bounded
  by the contradiction and by search, **not** by KB size.

The trigger for reconcile is **contradiction, not time**. You never re-read everything;
you fix what a change actually invalidated.

## Structure — one topic per file, plus an index

```text
knowledgebase/
├── INDEX.md          # one line per entry — the scannable map
├── decisions/        # decisions + why (append-only)
├── architecture/     # how non-obvious parts work (living)
├── issues/           # problems (open / solved / workaround)
└── shared/           # cross-cutting realities — single source of truth
```

**Why this layout** (over few-big-files or one-giant-file): it is the only shape where
"find the affected entry" and "reference a shared reality" have cheap, real mechanisms —
an `INDEX.md` scan and relative Markdown links — without loading the whole KB into context. It
mirrors the per-fact memory system already in use, so it is familiar.

## Content durability

Entries decay differently and are maintained differently:

- **decisions** — append-only, immutable. A reversal is a *new* entry that supersedes
  the old (linked), so the history of *why* is never destroyed.
- **issues** — append-only log; only `status` updates. Unsolved issues are recorded
  anyway, for the next time the symptom appears.
- **shared realities** — single source, updated in place.
- **architecture** — **living, rots fastest.** Kept high-altitude, cites `file:line`,
  and marked `verify-against-source`. **Why:** detailed prose about code goes stale the
  moment code changes; keeping it high-altitude and source-cited makes staleness cheap
  to detect and correct, and pushes details to where they stay true — the code.

## Reconciliation — single source of truth

When a fact is shared across many entries (e.g. "no HTTPS yet — no domain/cert"),
changing it later would mean hunting every copy. So cross-cutting realities live in **one**
`shared/` entry that other entries **reference** rather than restate.

**Why:** a change to a shared reality then becomes a **single edit**, and every
referencing entry is instantly current — no fan-out edits, no missed copies. A
safety-net search still catches stray duplicated copies (from before something was
recognized as cross-cutting) and folds them into the single source.

Reconciliation is **automatic**: the agent fixes contradictions it notices, including in
entries unrelated to the current task, and **surfaces those out-of-task edits
explicitly** so they can be rejected or adjusted.

## Triggers and enforcement

Content rules say *what* to record; they do nothing without a *when*. The when:

- **Checkpoint (always-on rule).** Before ending a turn that made a decision, added or
  changed a feature/subsystem, changed infrastructure, discovered an issue (solved or
  not), or invalidated an assumption — run the KB checkpoint. This rule lives in the
  project's `CLAUDE.md` so it is always in context.
- **Stop-hook reminder (planned).** A lightweight Stop hook that nudges the checkpoint
  at turn-end so task focus can't bury it. It must fire only "if anything changed" to
  avoid nagging on pure Q&A turns. **Not yet installed** — it touches `settings.json`
  and needs verifying against the current hook mechanism before adding.

The hook *reminds*; the skill *performs*. They do not collide because they do different
jobs — the hook never writes the KB itself.

## Where each piece lives

- **Full procedure** → the shared `knowledgebase` **skill** (portable across projects;
  loads only when used, so it costs almost nothing until needed).
- **Always-on pointer + checkpoint trigger** → a tiny block in each project's
  `CLAUDE.md`.
- **Reminder** → a Stop hook (planned).

**Why the split:** putting everything in `CLAUDE.md` weighs down every session; putting
everything in a skill means nothing is captured autonomously. The split gives low
always-on weight, the full procedure on demand, and reliable firing.

## Boundaries — KB vs personal memory

- **KB** — committed, about the project, authoritative, team- and agent-facing.
- **Personal memory** (`~/.claude/.../memory/`) — private, about the user and how they
  like to work.
- Rule of thumb: *committed + about the project → KB; private + about you → memory.*

Some knowledge currently in personal memory (e.g. project decisions) belongs in the KB.
The skill's `migrate-memory` mode moves it, asking before deleting anything; the first
migration is done interactively.

## Open items

- Install the Stop hook (verify hook mechanism first).
- Run `init` to bootstrap this repo's KB, then `migrate-memory` for the personal
  memories that are really project knowledge.
