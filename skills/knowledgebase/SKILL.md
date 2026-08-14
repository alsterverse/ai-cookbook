---
name: knowledgebase
description: Maintain a project's committed, team- and agent-facing knowledgebase — decisions and their rationale, how the project works, issues found, and cross-cutting shared realities — so future agent sessions inherit hard-won context. Use before ending a turn that made a decision, added or changed a feature/subsystem, changed infrastructure, established a convention, or discovered an issue (solved or not); when the user asks to update or sync docs for recent commits; or to bootstrap, review, or migrate project knowledge. Modes — checkpoint (default), sync, init, review, migrate-memory.
argument-hint: "[checkpoint|sync|init|review|migrate-memory]"
---

# Knowledgebase

Maintain a project's **committed, team- and agent-facing** knowledgebase (KB): the
knowledge that code and git history do *not* capture — why decisions were made, how
non-obvious parts work, issues hit and how (or whether) they were solved, and
cross-cutting realities the whole project leans on.

The KB exists to make future agentic sessions start informed. Optimize for that reader.

## Core principle: capture the un-recoverable

Record something only when **both** hold:

1. It is **non-obvious** *or* **significant** (a decision, an interface/behavior change,
   an infrastructure change, an issue, or a hard-won insight), **and**
2. A future agent **could not recover it** from code + git history + `CLAUDE.md`/`AGENTS.md`.

The second clause is the anti-bloat floor. Mechanical facts recoverable from the repo
are **not** captured: renames, version bumps, typo fixes, "a function now exists."
Capture the *why* and the *gotcha*, never the mechanical change itself.

Rejected approaches are high value and easy to lose — git barely shows abandoned work.
When a path was tried and dropped, record it and *why*.

**Conventions are not recoverable from diffs.** A change that establishes a rule future
code must follow — "use X, not the deprecated Y", a required wrapper/provider, a
mandatory install flag — passes the bar even when the change itself looks mechanical:
git shows *that* code changed, not that all new code must follow suit, nor the gotchas
that come with X. When a migration or fix implies "never do the old thing again",
record the rule and its gotchas.

## Where the KB lives (project-aware)

This skill is project-aware. Before doing anything, read the project's `CLAUDE.md` /
`AGENTS.md` for the KB location and any project-specific conventions. Default location
if unstated: `knowledgebase/` at the repo root.

## Structure

```text
knowledgebase/
├── INDEX.md          # one line per entry, grouped by type — the scannable map
├── decisions/        # decisions + why (append-only; supersede, don't rewrite)
├── architecture/     # how non-obvious parts work (living — verify against source)
├── issues/           # problems hit (open / solved / workaround)
└── shared/           # cross-cutting realities — single source of truth
```

- **`INDEX.md`** lets an agent see everything that exists without reading every file.
  Every new or removed entry updates it. One line: `- [Title](path) — one-line hook`.
- Link related entries inline with **standard relative Markdown links**:
  `[title](../folder/file.md)` (cross-folder links from a subfolder need `../`; a
  same-folder link is just `file.md`). **Never use `[[wikilink]]` syntax** — it does not
  render in editors or on GitHub. Linking to an entry that doesn't exist yet is fine — it
  marks a gap to fill later.

### Entry format

```markdown
---
title: <human title>
type: decision | architecture | issue | shared-reality
status: <issues: open | solved | workaround>  <architecture: verify-against-source>
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

<the knowledge>

**Why:** <for decisions — the reasoning, and any rejected alternative and why it lost>
```

- **Dates**: convert relative dates to absolute (`YYYY-MM-DD`). Do not invent a date —
  if unknown, ask or read it from git.
- **Body layout**: choose by length. A short entry with a few points is fine as a bullet
  list; a longer entry covering several distinct things reads better as `###` headers +
  description per item. Judgment per entry — don't force one style across all entries.

## Content types and durability

Different content decays differently — maintain it accordingly:

- **decisions** — append-only and immutable. A reversed decision gets a **new** entry
  that supersedes the old (link them with a relative Markdown link); the old one is not rewritten.
- **issues** — append-only log; only the `status` line changes as an issue is resolved.
- **shared realities** — updated **in place** at a single source (see Reconciliation).
- **architecture** — **living, rots fastest.** Keep it **high-altitude** (record the
  shape and the *why*, link to `file:line` for specifics — details belong in code).
  Mark `status: verify-against-source` and cite `file:line` so verifying is a quick
  check, not a re-read. Trust it only after confirming against source.

## Reconciliation (keep the KB internally true)

New knowledge can invalidate an assumption recorded **elsewhere**, even in entries
unrelated to the current task. Handle this automatically:

1. **Prefer single source of truth.** A cross-cutting reality (auth model, HTTPS/TLS
   availability, target OS/versions, network topology) lives in **one** `shared/`
   entry. Other entries **reference** it (a relative Markdown link) rather than restating it — so
   when it changes, the one edit makes every referencing entry current with no further
   edits.
2. **When a change invalidates a shared reality**, edit that one `shared/` entry, then
   check its **deviations** (entries that recorded an *exception* — an exception may now
   be moot or may now be the only special case).
3. **Safety-net search** for stray duplicated copies of the old assumption (phrasings
   may differ) and reconcile them — then fold them into the `shared/` entry so they
   don't drift again.
4. **Surface out-of-task edits explicitly** in the turn summary. Reconciling an
   unrelated entry is expected; report each one so the user can reject or adjust it.

## Modes

Determine the mode from the request. Default is **checkpoint**.

### checkpoint (default — automatic, incremental)

Run before ending a turn. Bounded to what this turn touched.

1. Did the turn hit a trigger? — made a **decision**; **added/changed** a feature or
   subsystem; changed **infrastructure**; **discovered an issue** (solved, worked
   around, or unsolved); **invalidated a recorded assumption**; **established a
   convention** (adopted X over deprecated Y, a required pattern or flag). If none,
   **stop** — do nothing.
2. For each trigger, apply the **capture bar** (above). Drop anything recoverable from
   the repo.
3. Write or update the entry in the correct folder; update `INDEX.md`; add relative
   Markdown links.
4. Run **Reconciliation** for any invalidated assumption.
5. Report what was written — especially any out-of-task reconciliation edits.

### sync `<commit range | "recent commits">`

Same as checkpoint, but seeded from git instead of the current turn — for changes made
outside this tool (manual edits, other tools, teammates' commits).

1. Diff the given range (`git log`/`git diff`). If no range given, ask which.
2. Identify triggers in the diff; apply the capture bar; write/update entries.
3. Run **Reconciliation**. Report what changed.

### init (bootstrap a fresh KB)

1. Scan structure: build/manifest files, source tree 2–3 levels, existing docs
   (`README`, `AGENTS.md`, `docs/`) — reuse, don't duplicate.
2. Create `knowledgebase/` with `INDEX.md` and the folders.
3. Seed **architecture** entries (high-altitude, `file:line` citations,
   `status: verify-against-source`), plus any **decisions**/**shared realities**
   evident from config. Do **not** invent — record only what the source supports.
4. Report the initial map for review.

### review (full reconcile pass — on demand)

1. Read `INDEX.md` and the entries.
2. For **architecture** entries (`verify-against-source`), verify against current
   source; repair or flag stale ones.
3. Find contradictions, duplicated assumptions (→ fold into `shared/`), and orphaned
   `INDEX.md` lines or dead relative links.
4. Report findings and fixes; ask before large rewrites.

### migrate-memory (move project knowledge out of personal memory)

Personal memory (`~/.claude/.../memory/`) is private and about the **user**; the KB is
committed and about the **project**. Project knowledge sitting in personal memory
belongs in the KB.

1. Read the memory files.
2. Classify each: **project** (committed + about the project + team-relevant → KB) vs
   **personal** (private + about how the user works → stays in memory).
3. Move project ones into the KB structure; update `INDEX.md`.
4. **Ask before deleting or trimming** any memory. Run the first migration
   interactively — the personal-vs-project call is judgment-heavy.

## Boundaries

- **KB** = committed, about the project, authoritative, team- and agent-facing.
- **Personal memory** = private, about the user and how they like to work.
- Rule of thumb: *committed + about the project → KB; private + about you → memory.*
