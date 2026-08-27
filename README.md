# skills

Two [Claude Code](https://claude.com/claude-code) skills I reach for on almost every session.
They are a pair: one decides **what to build and why**, the other decides **how to build it
cheaply**. Neither is interesting alone.

| Skill | Question it answers | Failure it prevents |
|---|---|---|
| [`orient`](orient/SKILL.md) | What is the highest-leverage next move, and how will I know if I was wrong? | Building the wrong thing well |
| [`ten-eighty-ten`](ten-eighty-ten/SKILL.md) | Which parts of this need my best model, and which don't? | Paying top-tier rates for mechanical work |

---

## Why these two

An agent session has a strong bias toward the task it walked in with. It will execute that task
competently, commit it cleanly, and report success — and none of that tells you it was worth doing.
Momentum feels like progress.

The second bias is quieter and more expensive: everything runs on whatever model the session
started on. A file rename and a security-sensitive refactor cost the same. They shouldn't.

`orient` attacks the first. `ten-eighty-ten` attacks the second.

```
  /orient
     │
     ├─ §0  read the ledger ──── close any prediction still open from last time
     ├─ §2  scan ────────────── returns a SLATE of 3, not a blessing for your plan
     ├─ §3  rank the slate ──── proceed │ spike │ reprioritize │ blocked │ reached
     ├─ §6  record the bet ──── Why Now + Falsifier + Cost & Payback + Runner-up
     ├─ §7  GATE ───────────────── a human sees the runner-up and can redirect
     │        │
     │        └──> /ten-eighty-ten     frame 10% · delegate 80% · verify 10%
     │                  │
     └─ §8  retro <─────┘        did it MATTER? → HIT/MISS back onto the ledger
                                  └── feeds the next §2 scan
```

The boundary between them is deliberate and there is no overlap:
**`orient` owns WHAT and WHY. `ten-eighty-ten` owns HOW and *does it work*. `orient` §8 owns the
separate question of whether it *mattered*.**

---

## orient

Orientation before execution. The core move is that it refuses to grade the task you arrived with
in isolation — the scan must return **three** candidates, one of which has to come from outside the
area you last worked in, and step 3 ranks them. A superlative over a set of one is always true, so
"is this the highest-leverage move?" is not a real question until a rival exists on paper.

What makes it more than a checklist:

- **A per-project `ORIENT-LEDGER.md`.** Append-only, sits beside your ADRs. Every orientation opens
  a bet and the *next* orientation closes it. Without this the loop has no memory and re-derives
  everything from zero each session.
- **A Falsifier on every decision.** "Why Now" must end in a claim that can come out false —
  *"this unblocks X"*, *"this moves coverage from N-of-M to K"*. A prediction that cannot lose
  teaches the next cycle nothing.
- **Five exits, not three.** `proceed` / `spike` / `reprioritize` — plus `blocked` (the real #1
  needs a human act, so name the blocker instead of quietly re-ranking to something you *can* do)
  and `reached` (this slice is done; stop manufacturing next items inside a finished area).
- **A local-maximum trigger.** A run of HITs with the North Star no closer is its own signal — the
  next slate is then *required* to carry a candidate from outside the mapped domain.
- **A data-domain protocol.** Before authoring into any bounded universe of facts, chart the full
  landscape from verified sources and size the covered/uncovered matrix. You cannot prioritize a
  gap you have not measured.

## ten-eighty-ten

Model-tiered delegation. The session model spends tokens only where judgment concentrates: the
first 10% (framing work into self-contained packets) and the last 10% (verification). The middle
80% runs on cheaper models.

The savings come from **context discipline** as much as from pricing — you read specs and verdicts,
never file dumps, and each executor's context stays minimal.

- **A gate before delegating at all.** Under 3 packets, orchestration overhead exceeds the savings:
  do it yourself and say so.
- **Required packet slots.** Goal / Context / Files / Non-goals / Acceptance / Report format. A
  packet the executor would need to ask a question about is under-specced — fix the packet, not the
  model tier.
- **Route by judgment-required, never by how important the code feels.**
- **A capped escalation ladder.** Retry once, bump one tier, then take over. There is no "retry
  until green".

---

## Install

Drop either directory into your skills folder:

```bash
git clone https://github.com/jamescazzetta/skills.git
cp -r skills/orient ~/.claude/skills/            # personal, all projects
cp -r skills/ten-eighty-ten ~/.claude/skills/
```

Project-scoped instead: copy into `.claude/skills/` inside the repo.

Then invoke by name — `/orient`, `/ten-eighty-ten` — or let the description trigger them. Invoking
**by name, in the open** matters more than it sounds: a sequence that was followed but never named
leaves a record that cannot show how the work was chosen.

`orient` will create its own `ORIENT-LEDGER.md` on first run. Ledgers are per project and
independent by default; a ledger may name others under `Linked ledgers:`, which are then read one
hop, read-only, and never written to.

---

## Notes

Written for Claude Code, and both skills assume its `Agent` tool for delegation and its model tiers
for routing. The ideas port to any agent harness with subagents and more than one model tier; the
specific tool names would need swapping.

No license file — all rights reserved. Fork freely for your own use; tell me if you improve them.
