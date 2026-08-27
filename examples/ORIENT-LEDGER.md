# ORIENT-LEDGER — Fernway  *(fictional example)*

> This is an illustrative ledger for a project that does not exist, included to show the shape of
> the loop over several cycles. Note the MISS in the second entry — a ledger containing only hits
> is a ledger that is not measuring anything.

Append-only. Written and read by `/orient`. Sits beside the ADRs deliberately: the ADRs record what
was **decided**, this file records what was **predicted** and whether the prediction held.

Never open a new entry while a landed item's `Verdict` is still `PENDING`.

---

## North Star  (confirmed: 2026-05-28)
Destination: An offline-first field guide a forager can trust with a decision they will eat —
  every edible verdict carrying its look-alike warnings and its source, on a phone with no signal.
Reached when: a forager completes an unassisted walk — open app, identify a plant they have never
  seen, reach a correct edible/inedible verdict — in airplane mode, twice, with different testers.
Linked ledgers: none

---

## 2026-06-02 — species record schema, before any more authoring
- Exit: proceed
- Why Now: 84 species are authored and every one of them will need re-authoring if the record shape
  changes; the look-alike field alone forces a self-referential relation the current flat rows
  cannot express. Fixing the shape now costs 84 migrations, fixing it at launch costs 212.
- Falsifier: if the next 30 species can be authored against the new schema with zero schema edits,
  the shape was right. Any schema edit inside those 30 means it was not.
- Cost & Payback: ~2 days, one migration script, touches every authored row / COMPOUNDING — every
  species after this one is cheaper, and the look-alike work in the backlog becomes possible at all.
- End-Shape: `species.schema.json` with look-alikes as typed relations, all 84 existing rows
  migrated and passing validation, authoring guide updated.
- Coverage: 84 of 212 target species — `docs/coverage/species-matrix.md`
- Runner-up (not doing): author 40 more species from the Cascades list. It is the obvious task and
  the one I opened the session intending to do — it loses because it makes the eventual migration
  50% more expensive and closes no structural gap.
- Verdict: HIT — 31 species authored before the first schema edit was needed, and that edit was an
  additive enum value rather than a shape change.

## 2026-06-19 — offline tile cache ahead of photo upload
- Exit: reprioritize
- Why Now: the roadmap's top item was photo upload, but three of five field testers reported blank
  maps in the field, which makes every other feature unreachable at the moment of use. Unblocks the
  entire offline premise the North Star rests on.
- Falsifier: field testers stop reporting blank maps.
- Cost & Payback: ~3 days, new cache layer + eviction policy / ONE-SHOT — chose it over a
  compounding rival because the premise itself was failing in the field.
- End-Shape: a tester can fly-mode a 20 km walk with no blank tiles, verified on two devices.
- Coverage: n/a
- Runner-up (not doing): the look-alike relation authoring pass, which the June 2 schema unblocked.
- Verdict: **MISS** — blank maps continued. The cache worked exactly as specified and the Falsifier
  still failed: it warmed only over wifi, and testers left home on cellular. The prediction was not
  wrong about the cache, it was wrong about *when caching happens* — which is a question nobody
  asked because the item arrived pre-framed as "build a cache". **Recalibration:** a field-reported
  symptom is not a specification. The next scan is required to name the mechanism it believes
  produces the symptom, and to say how that belief was checked.

## 2026-07-08 — how do toxic look-alikes get represented?
- Exit: spike
- Why Now: three authoring passes have stalled at the same place, and every proposed shape either
  buries the warning below the fold or duplicates it across every species that shares a look-alike.
  Nothing else in the authoring backlog can proceed until the shape is settled.
- Falsifier: the shape is settled when a species with four look-alikes, two of them fatal, can be
  authored and rendered without a special case.
- Cost & Payback: one delegated research pass over three published field guides / COMPOUNDING —
  it unblocks the whole authoring backlog.
- End-Shape: a note, not a feature. One page naming the chosen representation and the options
  eliminated, with the reason each lost.
- Coverage: n/a
- Runner-up (not doing): ship the four-species pilot without look-alike warnings behind a flag.
  Rejected on the grounds that an edible verdict without its warning is the one output this project
  must never produce, flag or no flag.
- Verdict: HIT — settled as a symmetric pairs table with severity on the edge, not a per-species
  list. **Eliminated, so it is not re-argued:** per-species duplication (drifts the moment one side
  is edited) and a free-text warning field (unsearchable, and impossible to gate on severity).

## 2026-07-21 — a botanist has to sign the edible verdicts
- Exit: blocked
- Why Now: 140 of 212 species now carry an edible/inedible verdict authored from published guides
  by someone who is not a botanist. Shipping any of them unreviewed is the project's one
  unacceptable failure — and no amount of engineering discharges it.
- Falsifier: a qualified reviewer signs off the first 40 verdicts with a correction rate under 5%.
  Above that, the authoring method itself is wrong and the remaining 100 need re-doing, not review.
- Cost & Payback: paid review, ~2 weeks calendar, no engineering / COMPOUNDING — a validated
  method makes every later verdict cheap; an unvalidated one makes all 212 worthless.
- End-Shape: 40 signed verdicts, a correction rate, and a written note on any systematic error
  found in the authoring method.
- Coverage: 140 of 212 verdicts authored, 0 reviewed — `docs/coverage/verdict-matrix.md`
- Runner-up (not doing): the offline search index. Genuinely useful and entirely doable this
  week — which is exactly why it was tempting. It is the meanwhile-item, not a promotion: taking
  it would have moved the coverage matrix while leaving the one unacceptable risk untouched.
- Blocker: needs a human — a qualified botanist under contract. **Smallest unblocking act:** email
  the two extension-service contacts from the May outreach and ask for a paid 40-verdict review.
- Verdict: PENDING

> **Note on this cycle's slate.** The June and July entries scored three HITs while `Reached when`
> got no closer — a local maximum: every candidate was coming from inside the authoring domain,
> because that is the domain with a coverage matrix, and a matrix makes its own gaps the most
> legible objects in the room. Per §8 the slate was required to carry a candidate from outside it.
> That candidate was the verdict review, and it won.
