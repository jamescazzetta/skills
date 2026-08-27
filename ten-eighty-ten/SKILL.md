---
name: ten-eighty-ten
description: Use when the user invokes /ten-eighty-ten, mentions the 10:80:10 principle, or explicitly asks to split a multi-part task across model tiers (Opus/Sonnet/Haiku) for token efficiency.
---

# Ten-Eighty-Ten — Model-Tiered Delegation

## Overview

You (the session model, top tier) spend tokens only where judgment concentrates: the first 10% (framing work into packets) and the last 10% (verification). The middle 80% executes on cheaper models via the Agent tool's `model` parameter. The savings come from keeping *your* context clean — you read specs and verdicts, never file dumps — and each executor's context minimal.

## Gate: delegate at all?

Delegate only if the task splits into 3+ self-contained packets, or contains at least one large mechanical packet (multi-file rename, test-table generation, codemod). Below that, do the work yourself and say so — orchestration overhead exceeds the savings. A tightly-coupled task with one unknown root cause is *not* three packets: keep the diagnosis, delegate only its recon and verification.

## Phase 1 — Frame

Delegate recon to haiku Explore agents; read only spec-critical files yourself. Then write one packet per delegable unit. Every packet has these REQUIRED slots:

- **Goal** — one sentence.
- **Context** — repo path, stack, and the files/docs the executor should read *itself* — name them as paths, don't re-summarize them. Inline only a snippet too small to point at. The executor sees only this prompt, nothing from your conversation.
- **Files** — exact paths in scope.
- **Non-goals** — what not to touch.
- **Acceptance** — runnable commands plus expected outcomes.
- **Report format** — file:line changes, command result summaries, deviations. No file dumps.

A packet the executor would need to ask a question about is under-specced. Fix the packet, not the model tier.

## Phase 2 — Execute

Route by how much unstated judgment the packet requires — never by how important the code feels. Tiers are **relative to your session model**; the names below are today's ladder:

| Tier | Route here when |
|---|---|
| `haiku` (cheap) | Fully determined by the packet: renames, boilerplate-from-example, tests from a case table, recon, verification gates |
| `sonnet` (default mid) | Well-specced single-module implementation, refactors, debugging with a reproduce-first protocol |
| `opus` → session tier | Judgment inside the packet: cross-file design, ambiguous legacy code, security-sensitive paths, work where a plausible-but-wrong result is hard to detect |

Rules:

- Set `model` explicitly on **every** Agent call — an omitted model inherits yours, silently, at zero savings. `model` is your only per-call lever here; the Agent tool has no `effort` parameter (that knob exists per node in scale mode).
- When unsure between tiers, start lower with a verifiable acceptance step; escalate on failure rather than paying up front.
- Independent packets launch in one message; use `isolation: "worktree"` only when write-sets overlap.
- While packets run, don't shadow or redo their work — wait for reports.

## Phase 3 — Verify

1. Dispatch a haiku **verification gate**: grep checks, typecheck, full test run, diff stat — reporting a pass/fail checklist so command output never enters your context.
2. Read the diffs of judgment-heavy packets yourself; spot-check mechanical ones.
3. Integration check: does the sum solve the original ask? Packets can pass individually and fail together.

**Quality mode (opt-in, high-stakes only).** When a wrong-but-plausible result is costly and hard to detect, add a semantic review by a **different model** than the builder — a model over-rates its own output, so self-review is weak. The reviewer re-runs the checks and cites file:line; it never certifies from pasted output. Highest stakes: poll three, take the majority. Skip it on mechanical or low-stakes packets — it isn't free.

On a failed packet, the escalation ladder is capped at two delegated attempts:

1. **Retry once** — SendMessage to the same agent with the specific failure (it keeps its context).
2. **Bump one tier** — fresh agent (next tier up, at most your session model), packet plus failure history.
3. **Take over** — fix it inline yourself, and note the packet was under-specced for next time.

After step 2 fails, you take over. There is no "retry until green".

## Scale mode (10+ packets)

For big fan-outs (migrations, audits, sweeps), use the Workflow tool instead of hand-dispatched agents: `pipeline()` the packets, each node carrying its own `{model, effort, schema}` — floor `effort` at `medium` so no node is under-thought. Write each brief to disk and dispatch a 2–4 line pointer ("read `<path>` and execute it; it is self-contained") — a pasted brief is paid as output once, then rides every later call. Re-read grounding from disk before each judgment artifact (a long run's context is lossy). The same gate, rubric, packet slots, and ladder apply.

## Common mistakes

| Mistake | Reality |
|---|---|
| Omitting `model` on a call | Runs on the top tier silently — the single most expensive mistake here |
| Pasting a doc's contents into a packet | Name the path; the executor reads it. Pasted, you pay it as output |
| "Opus to be safe" | Default sonnet; upgrade only on the named triggers. Opus is not automatically the ceiling — route relative to your session model |
| Retrying until green | Two delegated attempts, then take over |
| Trusting executor self-reports | Independent gate before done; for high stakes, a different model |
| Delegating a 2-packet task | Below the gate, solo is cheaper |
