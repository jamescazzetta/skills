---
name: orient
description: Use BEFORE starting any substantive task, and always when building into a data domain — to step back, scan product-vs-vision, rank a slate of candidate moves so you are solving the RIGHT problem, map the domain's full landscape and current coverage, simplify via first principles, then hand the top priority to ten-eighty-ten. Keeps a per-project ORIENT-LEDGER.md so each orientation reads the last one's prediction and closes it. Triggers on /orient, "step back", "what should we build next", roadmap/prioritization questions, or the start of a new work chunk.
---

# Orient — strategic orientation before tactical execution

## Overview

Executing the obvious next task is not the same as executing the RIGHT task. This
skill is the orientation layer that runs BEFORE `/ten-eighty-ten`: it verifies that
the thing you're about to build is the highest-leverage move toward the product
vision, that you understand the desired end-state, and — when building into a data
domain — that you have mapped the full landscape and know how much is already
covered. Only then do you hand the top priority to delegated execution.

The failure this prevents: **building the wrong thing well.** Momentum feels like
progress; a clean commit toward a low-leverage or mis-scoped target is still waste.

A superlative over a set of one is always true. Orientation that only ever grades the
task you walked in with is rationalization wearing prioritization's clothes — so this
loop ranks a slate, records a prediction that can come out false, and closes that
prediction next time round.

## When to use

- Before starting any substantive task (skip for trivial one-liners / mechanical edits).
- Whenever the next step is unclear, or you're tempted to grab the "convenient pothole."
- ALWAYS when building into a data domain you intend to map (ingredients, transforms,
  rules, taxonomies, any bounded universe of facts).

## The loop

### 0. Read the ledger (always first)
Orientation without memory is a fresh guess wearing a process. The ledger is that
memory: ONE `ORIENT-LEDGER.md` per project, beside the ADRs it cites. Find it with
`find . -name ORIENT-LEDGER.md -not -path '*/node_modules/*' | head -1` — THIS repo
only; if absent, create it in the ADR / decision directory (create that too if absent)
and fill State from §2's scan. Two zones, two mutation rules:

- **State** (pinned, top) — where we stand: North Star, coverage pointers, open
  blockers, links. Rewritten in place; re-date `confirmed:` on every rewrite.
- **Entries** (below, newest last) — the bets. Append-only: once an entry is in, only
  its Verdict line ever changes; wrong anywhere else means append a superseding entry
  and point the old Verdict at it. History is never edited, only outvoted.

Read State and the last entry — slug, Instead-of and Verdict are your §2 priors; you
are not starting cold. Then `grep -n 'Verdict    PENDING' <ledger>`: any PENDING entry
whose Guarantee has since landed gets its §8 micro-retro NOW, whoever opened it — and
never open a new entry while a landed item's Verdict is still PENDING.

**A ledger older than this format** uses `## North Star`, `Target`, `Why Now`, `End-Shape`,
`Runner-up`, a colon-form `Verdict:` — or no verdict field at all. Do NOT rewrite it; history
is never edited. Widen the sweep to find its open loops —
`grep -nEi 'verdict[: ]|pending|not run' <ledger>` — and read the last entry's own words,
since an unclosed bet there may be prose rather than a field. Close what has landed in
whatever form that entry already uses, write the NEXT entry in the format above, and convert
the pinned block to `## State` the first time you rewrite State. A format change is never a
reason to lose a loop.

**Format** — fixed-width labels, values starting at column 12, greppable by column:

```
## State  (confirmed: YYYY-MM-DD)
North Star <one line — the end-state THIS project is navigating toward>
Reached    <the observable that means this slice is DONE>
Coverage   <N/M · path for each live matrix — or none>
Blockers   <blocker + the smallest human act that unblocks it — or none>
Linked     <path> · us→them | they→us | ↔ · <what crosses the boundary> — or none

## O-<n> · <kebab-slug>  (YYYY-MM-DD)
Exit       proceed | spike | reprioritize | blocked | reached
Guarantee  <what the system GUARANTEES once this lands — not "what done looks like">
Why now    <the leverage claim that can come out false>
Falsifier  <the observable that would prove Why now wrong>
Cost       <rough spend> · ONE-SHOT | COMPOUNDING
Instead-of <runner-up slug — why it lost>
Coverage   <N/M · path to the matrix, or n/a>
Verdict    PENDING
```

`Verdict` is minted as `PENDING` and is the ONLY line that may later
change. Its whole value set is four states — anything else is unreadable to the next §0:

```
Verdict    PENDING
Verdict    HIT (YYYY-MM-DD) — <one line against Why now>
Verdict    MISS (YYYY-MM-DD) — <one line against Why now>
Verdict    DROPPED (YYYY-MM-DD) — <the higher lock that changed>
Verdict    SUPERSEDED → O-<m>
```

`O-<n> · slug` is the bet's one canonical name — it heads the ten-eighty-ten packet,
the commit and the ADR, so three sessions cannot call one piece of work three things.
Every value is ONE physical line, at most 120 characters after column 12, NEVER wrapped:
`awk 'length > 132 {print FNR": "length}' <ledger>` must print nothing. "One sentence" is not
a cap — a 755-character sentence obeys it — and a wrapped value breaks column-grep silently,
because the continuation line carries no label. Overflow is the signal, not the problem: it
means the clause belongs in an ADR row this entry cites. Each clause passes one test — would
it change a future decision? If not, cut it. **The ledger cites prose, it does not contain it**: a
finding that deserves paragraphs becomes an ADR row, and the entry carries its id.

**Linked ledgers** — declared on State's `Linked` line as direction + mechanism, never
a bare path: `../flavor-engine · they→us · their transform ids key our provenance`.
Read each declared ledger's State and last entry, strictly read-only: never append,
never close its PENDINGs, never take your Target from one — each project closes its
own loops. One hop, declared paths only; link only on a genuine cross-repo dependency.

### 1. Step back
Do not execute yet. Write the task you're ABOUT to do in one sentence — then question it.
That sentence is not the plan; it is candidate #1 on the §2 slate, and it enters §3 with
no privileges over its rivals.

### 2. Pre-flight & scan (delegate to a mid-tier agent)

**Docs are evidence, not ground truth.** A stale doc silently corrupts every step below
it — you'll compute leverage against a product that no longer exists.

Spawn a **sonnet** agent — mid-tier *relative to your session model*; that name is today's
ladder, not a fixed label — to read the vision / roadmap / architecture docs + the current
state, and report back:
- the DESIRED end-state — the shape of the "perfect solution" for THIS use case;
- where the product actually is now;
- **a SLATE of exactly 3 candidate next moves**, unranked, each with its strongest
  one-line case and a rough cost. One MUST be the §1 task. One MUST come from outside the
  area the last ledger entry worked in. A slate it cannot fill with three is itself a
  finding — say so rather than padding it;
- per doc consulted: last-modified date (`git log -1 --format=%cs -- <path>`), one code
  fact that confirms or contradicts it, and a FRESH/STALE verdict. A report with no dates
  is an incomplete report.

A report missing any of these four is under-specced: bounce it rather than reasoning over
the gap. You read the verdict, not the file dumps — keep your own context clean. Where a
doc is stale, weight the code over the doc and say so out loud; don't quietly reconcile them.

### 3. Evaluate: rank the slate
You are ranking the §2 slate — not grading the §1 task. If you never wrote down a rival,
you did not prioritize. Rank all three by leverage-per-cost, preferring COMPOUNDING work
at comparable leverage, then take exactly one exit and emit it verbatim before anything else:

`Exit: <proceed|spike|reprioritize|blocked|reached> — <the named gap, or the reason>`

- **Proceed** — your §1 task won the slate, and it closes a NAMED gap: an MVP capability,
  a blocking constraint, or a live risk. If you can't name the gap, you haven't earned
  this exit. Go.
- **Research / Spike** — you are NOT yet sure of the desired shape. Deep-dive one topic
  until the perfect-solution shape is unambiguous, THEN decide. A spike is learning, not
  building — it ends in a note, not a feature. Bound it by scope, not by clock: ONE topic,
  ONE delegated research pass. If that pass doesn't resolve the shape, that is itself the
  finding — narrow the topic or escalate, don't open a second tab. Log the entry with
  `Exit: spike` and the option it eliminated, so the next §0 doesn't re-argue it.
- **Reprioritize** — a rival on the slate beat your §1 task, or beat the roadmap's top
  item. Re-rank, state why in one line, proceed on the new top.
- **Blocked / Escalate** — the highest-leverage move exists and you cannot make it: it
  needs a human act (a tasting, a purchase, a credential, a judgement you are not
  authorized to make), an external dependency, or data that does not exist yet. Do NOT
  silently re-rank to something you CAN do — that is how look-busy work enters the roadmap
  wearing a leverage argument. Name the blocker, name the smallest human action that
  unblocks it, put both in front of the user, and only THEN ask whether the next-best item
  is worth doing meanwhile — as an explicit second choice, logged `Exit: blocked` with the
  real #1 named in the same entry so the next §0 still sees it.
- **Reached** — this slice is DONE: the ledger's `Reached` line holds, or the §4 coverage
  matrix is closed. Stop. Do not manufacture a next item inside a finished area; return to
  the vision for the next slice, or hand back to the user. Log `Exit: reached` and rewrite
  the State block.

The exit opens the entry: mint the next `O-<n>`, slug the winner, and write Exit, Why
now, Falsifier, Cost, Instead-of and `Verdict    PENDING` NOW, while the comparison is still live — not from
memory at §6. End Why now checkable ("unblocks X", "coverage 84/212 → 120/212"):
"improves quality" cannot come out false, so it teaches §8 nothing. A compounding
rival that lost to a one-shot gets its why on the Instead-of line.

### 4. Data-domain protocol (when building into data)
Understand the FULL SPECTRUM of the domain before adding to it. If the intent is to
MAP a domain, do this FIRST — before authoring a single new member:
1. **Chart the full landscape.** Enumerate the whole domain from *verified,
   scientifically-accepted sources* — not memory, not vibes. What are ALL the members?
2. **Measure coverage.** How much of that landscape do we already have? Produce the
   explicit covered / uncovered matrix, sized (N of M) — that size is the entry's
   Coverage line; write `N/M · path` now, not at §6.
3. **Consolidate into a lean, traceable report.** One structured doc per topic: the
   landscape, the coverage, the gaps — each claim cited to its source, each id
   traceable. Understandable and lean: a map, not a transcript.

**Reuse before re-deriving.** A previous pass may already have charted this domain — the
ledger's Coverage fields point at the reports. Extend and re-date the existing map;
re-enumerating from scratch every time is the opposite of compounding.

Building without the map is how you get messy-bucket features and ad-hoc,
non-systematic coverage. The map is also what makes the NEXT prioritization cheap.

### 5. Simplify (Feynman / first principles)
Before authoring the solution, make it EASY:
- **Feynman** — explain the problem and the intended solution in plain language, as
  if teaching a smart beginner. Every place the explanation stalls is a gap in your
  understanding; close it before you build. The Guarantee locks here — if you cannot
  write that one line un-hedged, the hedge IS the stall; close it before building.
- **First principles** — separate what is irreducibly true here from what is merely
  inherited assumption. Rebuild the solution up from the truths.

### 6. Confirm the record
By now the entry already exists — §3 wrote the bet, §4 the Coverage line, §5 the
Guarantee. This step confirms; it does not author. Read the entry back: every slot
filled, every line one sentence, no line needing a second. A slot you can't fill is a
step you haven't finished — go back to the step that owns it, don't pad the slot.
Reasoning that wants paragraphs goes into the ADR / decision register now, and the
entry cites its id. Not in chat scrollback: future-you and a reviewer reconstruct
the "why" from the entry and the rows it cites, without re-deriving it.

### 7. Gate, then execute via /ten-eighty-ten
Show the entry — Instead-of included, so the user can redirect without re-deriving your
comparison — and stop for confirmation. Say plainly if the move is one-way (hard to
reverse, invalidates published ids or provenance); a gate that renders an irreversible
decision identical to a reversible one only looks like oversight. This is the design
gate: the last cheap moment to change direction — everything past it spends tokens and
produces diffs.

On approval, invoke `/ten-eighty-ten` with the entry as the task statement, headed by
its `O-<n> · slug`; ten-eighty-ten's own gate decides whether the work is delegated or
done solo, so don't pre-decide that here. Carry Why now and Coverage into the packets'
Context — an executor that doesn't know what the work serves optimizes the wrong thing.

**Interrupt clause.** Don't re-orient mid-execution — but if a packet report shows the
Guarantee is unreachable as specified, or the gap Why now named is already closed, STOP
— and DISCARD: revert the in-flight work that existed only to serve the dead decision,
or the dropped bet ships anyway as a half-built diff. Supersede the entry — its Verdict
names the successor §1 opens — and re-enter there. That is not thrashing.

### 8. Micro-retro, then loop
When the item lands, ask the question the diff can't answer: did it deliver the leverage
the entry predicted? ten-eighty-ten's Phase 3 already verified it *works* — this asks
whether it *mattered*. Check BOTH predictions: the Falsifier (did the named observable
actually move?) and Cost (what did it really cost, and did the compounding payback show
up?). Then close the Verdict — the entry's one mutable line — with:

`Verdict    HIT (YYYY-MM-DD) — <one line against Why now>` — or `MISS`, same shape.
A bet the §7 gate rejected, or that the interrupt clause discarded, closes `DROPPED`; it is
still closed, and an entry that never resolves is a loop that blocks every later orientation.

A miss is data, not failure: it recalibrates the next §3, and repeated misses in one area
mean the §2 scan is reading the wrong signal. **A run of HITs with the North Star no
closer is its own signal** — that is a local maximum, and the next §2 slate must include a
candidate from outside the mapped domain.

Then return to step 0 before the next item. Never chain tactical tasks without
re-orienting at the boundary.

## Anti-patterns

| Smell | Reality |
|---|---|
| "It's the obvious next task, so I'll just do it" | Obvious ≠ highest-leverage. Run steps 2–3 first. |
| Ranking one candidate | A superlative over a set of one is always true. §2 returns three, or the scan failed. |
| "Leverage" with no cost on the other side | A gap named without a price is a wish. Rank by leverage-per-cost and log the cost you predicted. |
| A Why now that cannot come out false | Then §8 can only ever say HIT, and the loop never learns. Write the Falsifier. |
| Re-ranking because the real #1 needs a human | That is `blocked`, not `reprioritize`. Name the blocker and the unblocking act; a doable item is not automatically the top one. |
| Opening a new entry with a PENDING verdict on a landed item | Close it first. Unclosed loops are how calibration silently dies. |
| Composing the entry at §6 | Each line locked in §3–§5 the moment it resolved; §6 only confirms. Batched retrospection writes essays, not records. |
| A field that needs a second line | Over cap. The ledger cites prose, it does not contain it — the paragraph becomes an ADR row, the entry carries its id. |
| Editing a landed entry | Verdict is the only mutable line. Append a superseding entry and point the old Verdict at it. |
| Writing into a linked project's ledger | Read-only, one hop. Each project closes its own loops. |
| Trusting a roadmap doc you didn't date-check | Stale docs corrupt every step below them. Dates are a required field in the §2 report. |
| Mapping a domain from memory | Enumerate from verified sources; memory is lossy and biased. |
| Re-enumerating a domain already charted | The ledger points at the last map. Extend it; don't pay full price twice. |
| Building before measuring coverage | You can't prioritize a gap you haven't sized. |
| A giant exhaustive report | Lean and traceable wins. Fixed slots, a map, not the territory. |
| Skipping the Feynman pass | If you can't explain it simply, you don't understand it — and you'll build it wrong. |
| Skipping the gate because you feel confident | Confidence is cheapest to test before the tokens are spent, not after. |
| Re-orienting mid-execution | Orient at chunk boundaries, not on every step; thrashing is not diligence — but see §7's interrupt clause: "don't re-orient" never means "don't stop". |
| Stopping without discarding | The half-built diff serving a dead decision ships itself. §7's STOP includes DISCARD. |
| Spiking forever | One topic, one pass, ends in a decision — not an open research tab. |
| "It landed green, so it worked" | Green means correct, not high-leverage. Check the Falsifier. |
| Every retro a HIT, North Star no nearer | A local maximum. Force an outside-the-map candidate onto the next slate. |
| Manufacturing a next item in a finished area | That is `reached`. A navigator that cannot arrive sails past the harbour and calls it progress. |
