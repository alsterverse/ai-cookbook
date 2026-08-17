# Knowledgebase — design rationale

**Why** the knowledgebase (KB) is built the way it is. The rules and procedure live in
[SKILL.md](../SKILL.md); this document is the reasoning behind them and the roadmap. Read it
when changing *how the KB system works* — not when writing an entry.

## Purpose

Code and git record *what* changed, never *why* a decision was made, which alternatives
lost, how a load-bearing part actually works, or what gotcha cost an afternoon. That lives in
a person's head and is lost between sessions. The KB captures it so **future agentic sessions
start informed**.

## Two jobs, two bars

The KB serves two distinct jobs — *preserve the un-recoverable* and *anchor a shared concept*
— with different bars (defined in [SKILL.md → Core principle](../SKILL.md)). The design point
is that they are **separate on purpose**: a single "capture everything important" rule has no
objective floor (→ bloat) and no home for shared vocabulary (→ scattered, drifting
definitions). Splitting them gives capture an objective floor (recoverability) and gives
shared concepts a single canonical anchor.

## Two verbs, kept separate — capture vs reconcile

"Keep the *entire* KB updated" is a trap: no floor (bloat) and no ceiling (an agent can't
re-verify a large KB every turn, so it gives up and updates nothing). The fix is two rules
with different mechanics:

- **Capture** — *append* an entry when it clears the bar. Bounded by the bar.
- **Reconcile** — when new knowledge **contradicts** an existing entry, fix it. Bounded by
  the contradiction and by search, **not** by KB size.

The reconcile trigger is **contradiction, not time**. You never re-read everything; you fix
what a change actually invalidated.

## Why coverage needs a check of its own

Reconciliation is a **consistency** mechanism: it fires on *contradiction*. Nothing in it fires
on *absence*. A KB can be flawlessly self-consistent and still have no entry that owns a
load-bearing behaviour — and because `INDEX.md` lists the neighbouring topics, the hole reads as
covered. That is worse than a visibly empty KB, because it stops the reader from looking further.

The failure has one recurring shape, observed on this project's own bootstrap (2026-08-14).
Entries seeded from a source-tree scan come out **module-shaped** — one per subsystem, because
that is what the scan sees. Any behaviour that *crosses* subsystems then has no natural home, and
its pieces settle into whichever module entry happened to be open when each piece was learned.
The alert episode (detect → hold → investigate → rejoin) ended up split across three entries, and
its most operator-visible consequence — one alert slot per vehicle, so a low-battery alert
silently overwrites a human-with-weapon alert — was recorded in none of them.

Why **flows** specifically are the blind spot: a named contract prompts its own entry, because
writing the name makes you notice it needs a definition. A flow usually has **no name in the
code**, so nothing prompts anything. Naming the flow yourself is the whole fix — which is why job
B was widened from "concept, term, or contract" to include flows, rather than left to judgment.

Hence three additions, each targeting a different moment:

- **write time** — the *title test*: if a fact would surprise someone reading only the title and
  index hook, it is in the wrong entry. Cheap, per-entry, catches burial as it happens.
- **`init`** — seed along flows as well as modules, then a coverage check while the whole system
  is still in context. Bootstrap is the only moment the entire system is in one context window;
  a hole left there is paid for by every later session.
- **`review`** — an explicit coverage question, kept *separate* from the consistency checks so it
  cannot be quietly satisfied by them.

## Why the title test needed mechanical tells (and why it is re-run on append)

The title test was originally a write-time judgment: *would this fact surprise someone reading only
the title?* An audit of this project's own KB the same day it was bootstrapped (2026-08-14) found
four buried facts and seven under-promising names **despite** that rule being followed. The
post-mortem matters more than the fixes, because all four had one cause.

**Accretion, not misplacement.** Every buried fact entered an entry that was correctly named at the
time. The open-questions register grew inside an entry about how to read `concept.md`; the CoT
composition model grew inside an entry about transports; the threading/lock contract grew inside an
entry about the mission state machine. At each append the question an agent naturally asks is *"does
this fact relate to this entry's subject?"* — and the answer is honestly yes every time. The test
only bites if it is asked of the **entry** (*does the name still predict the whole file?*), which is
why the rule now says to re-run it on append, not on the fact.

**Judgment is the wrong instrument for a check run dozens of times.** "Would a reader be surprised"
is expensive and drifts. Comparing an entry's `###` headers to its name is free and would have caught
three of the four unaided — a header reading *"Planned: a third seam for telemetry"* under a file
named *"the hardware seam and who owns which side"* convicts itself. Hence the mechanical tells are
listed first and the surprise question last.

**Two further tells came out of the same audit.** A name shaped like a *sequence* (`…-path`,
`…-flow`, `…-transports`) invites a *model* to be filed under it, because a model plausibly "happens
along" the chain — that is how a Unity pose-renderer ended up inside a video-feed-path entry. And a
cross-reference added so a fact can be *found* is a confession: relatedness pointers are normal,
findability pointers mean the fact is in the wrong file.

**Filenames were simply out of scope before**, which the audit showed to be a gap: for an agent the
filename is the *first* signal (it greps, lists, and reads link paths long before it opens the file),
so it carries the same burden as the title.

**Why the anchor sweep was added to `init`/`review`.** The coverage check asked for owners of *flows*
and *gotchas*. Both missed items were neither: an event/message model and a threading contract are
**concepts**, leaned on by five entries each. Nothing in a source scan prompts them, no single entry
looks wrong without them, and job B's bar ("≥2 entries lean on it") had no procedural step that made
anyone enumerate such things. Now it does.

**Why `review` carries an explicit defect-class list.** The same audit continued over several rounds,
each prompted by a different question from the project owner, and each round found a *new class* of
defect the previous rounds had passed clean. That is the tell: an audit finds the classes it goes
looking for, and an agent asked to "check the KB" will check whatever criteria it happens to generate
that day — reliably finding the classes it was just told about, and reliably missing the others. The
list exists so coverage stops depending on that.

One class on it was only discovered because a *reader* asked, and it is invisible from inside the
entry that owns the knowledge. **Unlinked mention:** the rule "reference, don't restate" fires on
*restatement*; a bare mention of a concept another entry owns fires nothing — too short to feel like
duplication, long enough to strand a reader. Every placement check passes, because the fact really is
in the right file. It is only findable by reading from where a reader *starts* rather than from where
the knowledge *lives*, which is why the list says so explicitly.

The same episode produced a rule that was **considered and rejected**: annotating an ambiguous term
(the codebase calls two different mechanisms "watchdog") wherever it appears. That would mandate
duplicating a cross-cutting fact into every entry that merely mentions the word — the precise thing
single-source-of-truth exists to prevent — and it would go stale everywhere at once. Ambiguity is
resolved by naming the concept precisely **in the link text**, so the reader lands on the right entry
without anyone explaining the collision. The general form: when a defect can be fixed either by
adding an explanation or by fixing a reference, fix the reference.

**Deliberately not automated.** These checks are cheap, deterministic, and need the whole KB in one
context to judge "five entries lean on this" — fanning them out to parallel agents fragments exactly
the context that makes the judgment possible. The parts worth scripting are the ones that need no
judgment at all (dead links, `INDEX.md` orphans, entries nothing links to, filename↔title agreement);
the parts worth a human's attention stay inline.

## Why one-topic-per-file + an index

Over few-big-files or one-giant-file: it is the only shape where "find the affected entry"
and "reference a shared reality" have cheap, real mechanisms — an `INDEX.md` scan and
relative Markdown links — without loading the whole KB into context. It mirrors the per-fact
memory system already in use, so it is familiar.

## Why architecture entries stay high-altitude

Detailed prose about code goes stale the moment code changes. Keeping architecture entries
high-altitude and `file:line`-cited makes staleness cheap to detect and correct, and pushes
details to where they stay true — the code. (Per-type durability rules: [SKILL.md → Content
types and durability](../SKILL.md).)

## Why single-source-of-truth reconciliation

A fact shared across many entries (e.g. "no HTTPS yet — no domain/cert") would, on change,
mean hunting every copy. So a cross-cutting reality lives in **one** `shared/` entry others
reference. A change is then a **single edit** and every referencing entry is instantly
current — no fan-out, no missed copies. A safety-net search still folds stray copies back
into the single source.

## Enforcement — why the CLAUDE.md / skill / hook split

Content rules say *what* to record; they do nothing without a *when*.

- **Checkpoint pointer in `CLAUDE.md`** — always in context, so the turn-end checkpoint can't
  be forgotten. Tiny, so it barely weighs on a session.
- **Full procedure in the portable skill** — loads only when used, so it costs almost nothing
  until needed.
- **Stop-hook reminder (optional, per project)** — a lightweight Stop hook can nudge the
  checkpoint at turn-end so task focus can't bury it. It must fire only "if anything changed"
  to avoid nagging on pure Q&A turns. The hook *reminds*; the skill *performs* — they never
  collide because the hook never writes the KB itself.

Putting everything in `CLAUDE.md` weighs down every session; putting everything in the skill
means nothing fires autonomously. The split gives low always-on weight, the full procedure on
demand, and reliable firing.

## Roadmap

- Install the Stop-hook reminder (verify the current hook mechanism first).
- `migrate-memory`: move any project knowledge still sitting in personal memory into the KB.
