# ten-eighty-ten in practice — the June 2 schema decision  *(fictional example)*

The [ledger entry](ORIENT-LEDGER.md) for 2026-06-02 passed the design gate as:

> **Target:** species record schema with look-alikes as typed relations, 84 rows migrated.
> **Why Now:** 84 migrations now vs 212 at launch; the look-alike relation is impossible in flat rows.
> **End-Shape:** `species.schema.json`, all 84 rows migrated and validating, authoring guide updated.

Here is what `/ten-eighty-ten` did with it.

---

## Gate: delegate at all?

Four self-contained packets, one of them a large mechanical transform. **Above the gate — delegate.**
(Had this been two packets, the correct answer is to do it inline and say so.)

## Phase 1 — Frame (session model, ~10%)

The schema design itself is *not* delegated. It is the one piece where a plausible-but-wrong result
is expensive and hard to detect — a shape that looks fine and fails on the fifth fatal look-alike.
That judgment stays home. Everything downstream of the shape is delegable.

Recon went to a `haiku` Explore agent: *"list every file that reads or writes a species row, and
every test that asserts on its shape."* Verdict came back as 11 paths. Those paths — not their
contents — went into the packets.

## Phase 2 — Execute (80%)

| # | Packet | Tier | Why this tier |
|---|---|---|---|
| 1 | Write `species.schema.json` from the agreed shape, with the look-alike pairs table and severity on the edge | **opus** | Judgment inside the packet — the relation model is the whole decision |
| 2 | Migration script: 84 flat rows → new shape, idempotent, dry-run flag | **sonnet** | Well-specced single-module implementation |
| 3 | Validation suite from the case table (12 cases incl. 4 look-alikes, 2 fatal) | **haiku** | Fully determined by the packet — tests from a table |
| 4 | Update the authoring guide's worked example to the new shape | **haiku** | Mechanical, one file, example given |

One packet, in full, so the required slots are visible:

> **Goal** — Migrate all 84 authored species rows from the flat shape to `species.schema.json`.
>
> **Context** — Node/TS repo at `~/dev/fernway`. Read `species.schema.json` (packet 1's output),
> `docs/authoring-guide.md` §3, and `data/species/*.json`. You see only this prompt — nothing from
> the orchestrating conversation.
>
> **Files** — `scripts/migrate-species.ts` (new), `data/species/*.json` (84 files, in place).
>
> **Non-goals** — Do not touch the schema itself, the renderer, or any test file. Do not author new
> species. Do not "fix" data you believe is wrong — report it instead.
>
> **Acceptance** — `pnpm migrate:species --dry-run` reports 84/84 convertible with zero warnings;
> after a real run, `pnpm validate:species` exits 0; `git diff --stat` shows exactly 84 changed
> files.
>
> **Report format** — file:line for the script's entry point, the dry-run summary line, the
> validate exit code, and any row you declined to convert with the reason. No file dumps.

Packets 2–4 launched in one message. Packet 1 had to land first — 2 and 3 read its output.

## Phase 3 — Verify (session model, ~10%)

1. A `haiku` **verification gate** re-ran validate + the suite + `git diff --stat` and reported a
   pass/fail checklist, so no command output entered the orchestrator's context.
2. The orchestrator read packet 1's diff itself — that is the judgment-heavy one — and spot-checked
   the migration.
3. **Integration check:** packets can pass individually and fail together. Here the question was
   whether a migrated row still renders, which no single packet owned.

Packet 3 failed first pass: two fatal-look-alike cases asserted the old field name. Per the ladder,
**retry once** with the specific failure — same agent, which still had its context — and it passed.
No tier bump needed. The ladder caps at two delegated attempts; after that the orchestrator takes
over and notes the packet was under-specced.

---

## What the pairing bought

`orient` established that the schema — not the 40 species the session opened intending to author —
was the move, and recorded a Falsifier that made the claim checkable 31 species later.
`ten-eighty-ten` then bought that decision at roughly a quarter of the tokens a single top-tier
session would have spent on the same four packets, with the one genuinely hard judgment kept home.

Neither skill would have produced that outcome alone. `orient` without `ten-eighty-ten` picks the
right target and overpays for it; `ten-eighty-ten` without `orient` builds the wrong thing
efficiently.
