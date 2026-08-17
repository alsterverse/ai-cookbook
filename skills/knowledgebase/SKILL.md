---
name: knowledgebase
description: Maintain a project's committed, team- and agent-facing knowledgebase — decisions and their rationale, how the project works, issues found, and cross-cutting shared realities — so future agent sessions inherit hard-won context. Use before ending a turn that made a decision, added or changed a feature/subsystem, changed infrastructure, or discovered an issue (solved or not); when the user asks to update or sync docs for recent commits; or to bootstrap, review, or migrate project knowledge. Modes — checkpoint (default), sync, init, review, migrate-memory.
argument-hint: "[checkpoint|sync|init|review|migrate-memory]"
---

# Knowledgebase

Maintain a project's **committed, team- and agent-facing** knowledgebase (KB): the
knowledge that code and git history do *not* capture — why decisions were made, how
load-bearing parts actually work, issues hit and how (or whether) they were solved, and
cross-cutting realities the whole project leans on.

The KB exists to make future agentic sessions start informed. Optimize for that reader.

The *why* behind this system's own design (and its roadmap) lives in
[references/design.md](references/design.md) — read it when changing **how the KB system
works**, not when writing an entry.

## Core principle: two jobs

The KB does **two** jobs for a future agent. Something earns an entry if it serves
**either** — they have **different bars**, and conflating them is what makes a KB both
bloated *and* full of holes.

**A — Preserve the un-recoverable.** Knowledge a future agent could **not** recover from
code + git history + `CLAUDE.md`/`AGENTS.md` *and* that would change how it acts: a decision
and its *why*, how a load-bearing part *actually* behaves, an issue and how (or whether) it
was solved, a rejected approach and why it lost (git barely shows abandoned work — high
value, easily lost). The bar is **recoverability**, and it is deliberately objective — do
not adjudicate whether something feels "obvious" (that is subjective and drifts per reader);
ask the concrete question: *could a competent reader reconstruct this from the repo as it
stands?* If yes, leave it out. That is the anti-bloat floor, and it is what rules out
mechanical facts — renames, version bumps, typo fixes, "a function now exists". Capture the
*why* and the *gotcha*, never the mechanical change.

**B — Anchor a shared concept or a cross-cutting flow.** A load-bearing concept, term, or
contract the system leans on in **more than one place** — "Decision", the CoT detail contract,
the hardware seam — gets **one canonical entry** that other entries link instead of restating.
The bar is *"a thing ≥2 entries or ≥2 subsystems need to refer to?"* — **not** recoverability.
An anchor earns its file even when its definition already sits in a docstring, because its value
is **structural**: one place to understand the concept, one link target, and discoverability
that the concept exists at all. This is why `shared/` exists; an anchor can also be an
`architecture/` entry when it explains how a part works.

**Flows are anchors too, and they are the ones most often missed.** An operational sequence
that crosses subsystems — detect → alert → hold → investigate → resume; receive → arm →
confirm → run; request → prepare → announce → stop — is exactly as load-bearing as a named
contract. But unlike a contract it usually has **no name in the code**, so nothing prompts you
to give it a file. Entries organised per code module then scatter the flow across every module
it touches and **no entry owns it**. Name the flow yourself, give it one entry, and let the
per-module entries link to it.

When a turn introduces — or starts leaning on — such a concept or flow, give it its anchor
entry then and there. Do **not** inline the explanation in one entry and reference it from
another; that is the duplication Reconciliation exists to prevent.

**An anchor only works if every mention reaches it.** The first time an entry names something
another entry owns, **link it**, and make the link text name *the concept* — "the connection-loss
watchdog", not "see liveness". Restating a concept triggers the duplication rule and gets caught; a
bare **mention** triggers nothing, which is exactly why mentions are what get left stranded. A term
worth using is worth linking once. Conversely, if you would have to explain a term inline, you are
either missing an anchor or writing in the wrong entry.

This is also how an **ambiguous term** is handled — by the link, never by a note about the ambiguity.
Where the source uses one word for two mechanisms, name the concept precisely in the link text ("the
connection-loss watchdog", "the grip watcher") so the reader lands on the right entry. Do **not**
explain the collision in the entries that merely mention it: that duplicates a cross-cutting fact
across every such entry — exactly what single-source-of-truth exists to prevent — and it goes stale
everywhere at once.

**Where a fact goes — the title test.** A reader who sees only an entry's **filename**, title and
`INDEX.md` hook must be able to predict what is inside. If a fact would *surprise* that reader,
it is in the wrong entry: either it belongs in the entry whose title does advertise it, or it
needs an entry of its own. Load-bearing knowledge buried under a narrow name is worse than
missing — the index makes the topic look covered, so nobody looks further.

Three mechanical tells. Use them **before** reasoning about what would surprise a reader; they
catch burial without needing judgment:

- **Headers vs name.** List the entry's `###` headers. A header naming something the name does not
  imply is either misplaced or proof the entry now covers two subjects.
- **A name that promises a sequence while the file owns a model.** `…-path`, `…-flow`,
  `…-transports`, `…-stack` promise a chain or a carrier. If the file *also defines* something — a
  component, a wire format, a contract — that definition needs its own entry.
- **A pointer added for findability.** If you add "see X" from an unrelated entry so a fact can be
  *found* (not because the two are genuinely related), the fact is in the wrong file. Move it, then
  delete the pointer.

**Re-run this on the entry — not on the fact — every time you append.** Burial happens through
accretion far more often than through a single bad placement: an entry that was correctly named
when created grows a second subject one fact at a time, and each fact individually does "relate to
the topic". The question is never *"does this fact belong to this subject"* but *"does the name
still predict the whole file"*.

## Where the KB lives (project-aware)

This skill is project-aware. Before doing anything, read the project's `CLAUDE.md` /
`AGENTS.md` for the KB location and any project-specific conventions. Default location
if unstated: `knowledgebase/` at the repo root.

## Structure

```text
knowledgebase/
├── INDEX.md          # one line per entry, grouped by type — the scannable map
├── decisions/        # decisions + why (append-only; supersede, don't rewrite)
├── architecture/     # how load-bearing parts work (living — verify against source)
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

- **Filename**: kebab-case, mirroring the title. It is what a reader greps and what every link
  shows, so it carries the same burden as the title — see the **title test**. Prefer a name that
  covers the whole file over a narrower one that reads better.
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
   around, or unsolved); **invalidated a recorded assumption**. If none, **stop** — do
   nothing.
2. For each trigger, apply the **capture bar** — *both* jobs (above): drop repo-recoverable
   *facts*, but still anchor a shared concept that ≥2 entries or subsystems lean on.
3. Write or update the entry in the correct folder — placed by the **title test**, not by
   whichever file you happened to edit. If the knowledge belongs to a flow that crosses
   subsystems, it goes in that flow's entry (create it if missing), not in the module entry
   nearest to today's task. **If you appended to an existing entry, re-run the title test on that
   entry** — that is where burial comes from. Update `INDEX.md`; add relative Markdown links.
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
3. **Seed along two axes, not one.** A source-tree scan yields *module-shaped* entries only, so
   list the system's **flows** separately — what happens end to end when it is actually used —
   and give each an owning entry (see job B). Then seed **architecture** entries (high-altitude,
   `file:line` citations, `status: verify-against-source`), plus any **decisions**/**shared
   realities** evident from config. Do **not** invent — record only what the source supports.
4. **Coverage check before reporting.** For every flow and every load-bearing gotcha you found,
   name the entry that owns it. Then **sweep for anchors**: list the concepts, contracts and
   formats that ≥2 entries lean on, and name the owner of each. A concept that is neither a flow
   nor a gotcha is the easiest thing to leave ownerless — an event/message model, a threading or
   locking contract, a naming scheme — because nothing in a source scan prompts it and no single
   entry looks wrong. Anything owned by no entry, or buried in one whose name fails the **title
   test**, is misplaced. Fix it now, while the whole system is still in context; this is the
   cheapest it will ever be.
5. Report the initial map for review.

### review (full reconcile pass — on demand)

1. Read `INDEX.md` and the entries.
2. For **architecture** entries (`verify-against-source`), verify against current
   source; repair or flag stale ones.
3. Find contradictions, duplicated assumptions (→ fold into `shared/`), and orphaned
   `INDEX.md` lines or dead relative links.
4. **Check coverage, not just consistency.** Ask the one question the checks above cannot
   answer: *what load-bearing knowledge has no owning entry?* Look for a flow split across
   module entries with no entry owning it, a **concept ≥2 entries lean on** with no anchor, and a
   gotcha buried under a name that does not advertise it (**title test** — run its header-vs-name
   tell over every entry; it is mechanical and finds most burial). A KB can be perfectly
   self-consistent and still have holes — and `INDEX.md` will make those holes read as covered.
5. Report findings and fixes; ask before large rewrites.

**Run against this list, not against what occurs to you.** A review finds the defect classes it goes
looking for, so the list is the instrument — do not shorten it because the KB "looks fine".

| # | Defect | How to find it |
|---|---|---|
| 1 | **Stale** architecture entry | verify `file:line` citations against source |
| 2 | **Contradiction / duplicated assumption** | two entries stating the same cross-cutting fact → fold into `shared/` |
| 3 | **Burial** — load-bearing fact under a name that does not advertise it | headers-vs-name tell, over every entry |
| 4 | **Under-promising name** — filename/title narrower than contents | read each name, then each file's headers |
| 5 | **Ownerless** flow, or concept ≥2 entries lean on | anchor sweep — enumerate what is referenced repeatedly, name each owner |
| 6 | **Unlinked mention** — a term used where its owning entry is not reachable | grep each anchor's key term across the KB; a hit outside the owner should reach *an* owner by link |
| 7 | **Findability pointer** — "see X" added so a fact can be found | any cross-reference that exists for discovery, not relatedness → the fact is misfiled |
| 8 | **Mechanical** — dead links, `INDEX.md` orphans, entries nothing links to | script it; no judgment required |

Classes 3–7 are the ones a self-consistent KB hides best, and 6 is invisible from inside the owning
entry — it can only be found by reading from where a *reader* would start. Class 6 needs judgment on
the way out: a concept can legitimately have two owners (a decision for the *why*, a flow for the
*how*), so reaching either is fine, and not every mention needs a link — only enough that a reader
meeting the term is never stranded. Do not mass-link to satisfy the grep.

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
