# Worked example — Fernway

A fictional project: an offline-first field guide to edible wild plants. It is invented, but the
shapes are real ones — a data domain with a coverage matrix, a decision that had to be made before
authoring could continue, a prediction that turned out wrong, and a #1 priority no amount of
engineering could discharge.

| File | Shows |
|---|---|
| [`ORIENT-LEDGER.md`](ORIENT-LEDGER.md) | Four orientations over three months: a compounding `proceed`, a `reprioritize` that came out a **MISS**, a `spike` that eliminated two options so they stop being re-argued, and a `blocked` exit |
| [`packets.md`](packets.md) | What `/ten-eighty-ten` did with one of those decisions — the gate, tier routing, a packet in full, and the failed packet that got retried once |

## What to look at

**The MISS.** The offline tile cache worked exactly as specified and the Falsifier still failed —
testers left home on cellular, so the cache never warmed. The recalibration is the useful part: *a
field-reported symptom is not a specification.* Without a Falsifier written down in advance, that
cycle scores as a HIT ("we shipped the cache") and teaches nothing.

**The runner-up on every entry.** Each records what came second and why it lost. This is what makes
the design gate a real decision rather than a rubber stamp — a reviewer who disagrees can redirect
without re-deriving the comparison, and a future session can see which roads were already
considered and rejected.

**The `blocked` exit.** The genuine #1 was a botanist signing off 140 edible verdicts. The tempting
move was the offline search index — useful, doable that week, and it would have moved the coverage
matrix while leaving the one unacceptable risk untouched. Logging it as `blocked` with the smallest
unblocking act named is what stops a doable item from being promoted just because it is doable.

**The local-maximum note at the end.** Three HITs in a row while the North Star got no closer, all
of them from inside the one domain that had a coverage matrix — because a matrix makes its own gaps
the most legible objects in the room. That is the trap the loop has to be able to see from the
inside.
