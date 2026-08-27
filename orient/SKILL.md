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
memory: ONE append-only `ORIENT-LEDGER.md` **per project**, living beside §6's
decision records.

Find this project's ledger — search THIS repo only:
`find . -name ORIENT-LEDGER.md -not -path '*/node_modules/*' | head -1`

- **No ledger** — create it in the repo's ADR / decision directory (create that too if
  absent) using the format below, fill the North Star from §2's scan, then go to §1.
- **Ledger found** — read the pinned block and the last entry, then
  `grep -n 'Verdict: PENDING' <ledger>`. Any PENDING entry whose End-Shape has since
  landed gets its §8 micro-retro NOW, before you orient again. Closing another session's
  loop is your job, not an intrusion — unclosed loops are how calibration silently dies.

The last entry's Target, Runner-up and Verdict are your priors for §2: you are not
starting cold. Never open a new entry while a landed item's Verdict is still PENDING.

**Linked ledgers.** Ledgers are per-project and independent by default. If — and only
if — this project's pinned block names others under `Linked ledgers:`, read the pinned
block and last entry of each of those too, as context for a genuine cross-repo
constraint. Strictly read-only:
- never append to, close a PENDING in, or edit a linked project's ledger — each project
  closes its own loops, and its Verdicts calibrate its own scans, not yours;
- a linked entry is a constraint or a prior, never your Target — you cannot orient
  another project from here;
- linkage is neither symmetric nor inherited: follow only the paths THIS ledger names,
  one hop, and never go hunting for ledgers a project has not declared.

Adding a link is a deliberate act — append a path under `Linked ledgers:` only when a
decision here genuinely depends on that project's direction, and say why in one line.

**Ledger format** — pinned block at the top, entries appended below, newest last:

```
## North Star  (confirmed: YYYY-MM-DD)
Destination: <one line — the end-state THIS project is navigating toward>
Reached when: <the observable that means this slice is DONE>
Linked ledgers: <path — why it constrains us> | none

## YYYY-MM-DD — <target, <=10 words>
- Exit: proceed | spike | reprioritize | blocked | reached
- Why Now: <leverage claim that can come out false>
- Falsifier: <the observable that would prove Why Now wrong>
- Cost & Payback: <rough spend> / ONE-SHOT | COMPOUNDING
- End-Shape: <testable done>
- Coverage: <N of M + path to the matrix, or n/a>
- Runner-up (not doing): <what lost, and why>
- Verdict: PENDING
```

Fixed field order is deliberate: it makes the loop greppable, so a session can *verify*
it closed the loop instead of believing it did.

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
- **Reached** — this slice is DONE: the ledger's `Reached when` holds, or the §4 coverage
  matrix is closed. Stop. Do not manufacture a next item inside a finished area; return to
  the vision for the next slice, or hand back to the user. Log `Exit: reached` and update
  the North Star block.

### 4. Data-domain protocol (when building into data)
Understand the FULL SPECTRUM of the domain before adding to it. If the intent is to
MAP a domain, do this FIRST — before authoring a single new entry:
1. **Chart the full landscape.** Enumerate the whole domain from *verified,
   scientifically-accepted sources* — not memory, not vibes. What are ALL the members?
2. **Measure coverage.** How much of that landscape do we already have? Produce the
   explicit covered / uncovered matrix, sized (N of M).
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
  understanding; close it before you build. If you cannot write §6's End-Shape without
  hedging, that hedge IS the stall — close it before building, not after.
- **First principles** — separate what is irreducibly true here from what is merely
  inherited assumption. Rebuild the solution up from the truths.

### 6. Document the decision
Write the record BEFORE delegating anything — as a ledger entry, and in the ADR /
decision log where it belongs. A slot you can't fill is a step you haven't finished:

- **Target** — what exactly are we building?
- **Why Now** — the leverage argument in one line, ending in a claim that can be CHECKED
  when it lands: "this unblocks X", "this moves coverage from N-of-M to K", "this cuts
  the next item's cost from A to B". "It's next on the list" and "it improves quality"
  are not claims — they cannot come out false, so they teach §8 nothing.
- **Falsifier** — the observable that would prove Why Now wrong.
- **Cost & Payback** — rough spend (files touched, delegated packets, whether new
  external evidence must be gathered), plus ONE-SHOT or COMPOUNDING: does the value land
  once, or does this make the next N items cheaper (a harness, a map, a schema)? If you
  picked a one-shot over a compounding rival, say why on the same line.
- **End-Shape** — what does "done" look like, concretely enough to test against?
- **Coverage** — link to the N-of-M matrix, if applicable.
- **Runner-up** — what came second on the slate, and why it lost.

Not in chat scrollback. Future-you and a reviewer should reconstruct the "why" without
re-deriving it.

### 7. Gate, then execute via /ten-eighty-ten
Show the record — including the **Runner-up**, so the user can redirect without
re-deriving your comparison — and stop for confirmation. Say plainly if the move is
one-way (hard to reverse, invalidates published ids or provenance); a gate that renders
an irreversible decision identical to a reversible one only looks like oversight. This is
the design gate: the last cheap moment to change direction — everything past it spends
tokens and produces diffs.

On approval, invoke `/ten-eighty-ten` with the §6 record as the task statement; its own
gate decides whether the work is delegated or done solo, so don't pre-decide that here.
Carry Why Now and Coverage into the packets' Context — an executor that doesn't know what
the work serves optimizes the wrong thing.

**Interrupt clause.** Don't re-orient mid-execution — but if a packet report shows the
End-Shape is unreachable as specified, or the gap the Why Now named is already closed,
STOP. That is not thrashing. Log it on the entry and re-enter at §1.

### 8. Micro-retro, then loop
When the item lands, ask the question the diff can't answer: did it deliver the leverage
§6 predicted? ten-eighty-ten's Phase 3 already verified it *works* — this asks whether it
*mattered*. Check BOTH predictions: the Falsifier (did the named observable actually
move?) and Cost & Payback (what did it really cost, and did the compounding payback show
up?). Then replace `Verdict: PENDING` on that ledger entry with:

`Verdict: HIT|MISS — <one line against Why Now>`

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
| A Why Now that cannot come out false | Then §8 can only ever say HIT, and the loop never learns. Write the Falsifier. |
| Re-ranking because the real #1 needs a human | That is `blocked`, not `reprioritize`. Name the blocker and the unblocking act; a doable item is not automatically the top one. |
| Opening a new entry with a PENDING verdict on a landed item | Close it first. Unclosed loops are how calibration silently dies. |
| Writing into a linked project's ledger | Read-only, one hop. Each project closes its own loops. |
| Trusting a roadmap doc you didn't date-check | Stale docs corrupt every step below them. Dates are a required field in the §2 report. |
| Mapping a domain from memory | Enumerate from verified sources; memory is lossy and biased. |
| Re-enumerating a domain already charted | The ledger points at the last map. Extend it; don't pay full price twice. |
| Building before measuring coverage | You can't prioritize a gap you haven't sized. |
| A giant exhaustive report | Lean and traceable wins. Fixed slots, a map, not the territory. |
| Skipping the Feynman pass | If you can't explain it simply, you don't understand it — and you'll build it wrong. |
| Skipping the gate because you feel confident | Confidence is cheapest to test before the tokens are spent, not after. |
| Re-orienting mid-execution | Orient at chunk boundaries, not on every step; thrashing is not diligence — but see §7's interrupt clause: "don't re-orient" never means "don't stop". |
| Spiking forever | One topic, one pass, ends in a decision — not an open research tab. |
| "It landed green, so it worked" | Green means correct, not high-leverage. Check the Falsifier. |
| Every retro a HIT, North Star no nearer | A local maximum. Force an outside-the-map candidate onto the next slate. |
| Manufacturing a next item in a finished area | That is `reached`. A navigator that cannot arrive sails past the harbour and calls it progress. |
