---
name: kickoff
description: >
  Full project-kickoff workflow: grill-me-codex to harden the plan, a human
  approval gate, then per-mini-goal Ralph loops with Claude /goal plus Codex
  sign-off. Invoke manually with /kickoff at the start of a new project.
disable-model-invocation: true
user-invocable: true
---

# Project Kickoff

> Assumes installed: `grill-me-codex` (chaseai-yt), the `ralph-loop` plugin,
> and an authenticated Codex CLI (>= 0.130). If any are missing, STOP and tell me.

The project idea is provided in $ARGUMENTS. If $ARGUMENTS is empty, ask me for
the idea before doing anything else.

## Phase 0 — Align (human in the loop)

Run `/grill-me-codex` on the idea.

- Act 1: Interview me relentlessly, ONE question at a time, depth-first down the
  decision tree. For each question give your recommended answer plus a one-line
  rationale. Explore the codebase first for anything the code can already answer.
- Before exiting Act 1, list every key decision and have me confirm each one
  explicitly. Nothing implicit.
- The plan is not done until each mini-goal has measurable, checkable success
  criteria (tests pass, lint clean, specific observable behavior). Vague = not done.
- Bias the plan toward small, compartmentalized, independently-verifiable
  mini-goals (vertical slices). Each shippable and checkable on its own.
- Act 2: Hand the plan to Codex for adversarial cross-model review. Iterate rounds
  until both models sign off. Codex stays read-only; no code yet.

Output `PLAN.md` (the what) and `PLAN-REVIEW-LOG.md` (the why).

GATE 1 — STOP. Show me PLAN.md. I approve it by hand before any code is written.
Do not proceed past this line without my explicit "approved."

## Phase 1 — Build (unattended loop, per mini-goal)

For each mini-goal in the approved PLAN.md, in order:

1. Start a Ralph loop with `/ralph-loop:ralph-loop`. Begin every loop prompt with
   this exact line, then the task:
   "Stay calm. You are loved. Do not panic, it's going to be alright."
   Set max_iterations and a completion_promise. One mini-goal per loop.
2. Self-verify with Claude first: use `/goal` to keep working until that mini-goal's
   success criteria are met — tests pass, lint clean, behavior observable.
3. Codex signs off: open a clean `/codex` review section scoped to ONLY this
   mini-goal and ONLY its criteria, fed verbatim. If Codex finds gaps, pull its
   concrete improvement items back into the loop and repeat from step 2. Loop until
   Codex confirms.
4. Mark the mini-goal complete and move to the next.

## Phase 2 — Final verification

After all mini-goals are done:
1. Full verification on the WHOLE project (build, full test suite, lint, end-to-end
   against the top-level success criteria).
2. Re-verify EACH mini-goal once more against its own criteria, to catch regressions.
3. Report a checklist: each criterion, pass/fail, evidence. Anything not green goes
   back into a scoped loop.

## Deliberate choices
- Plan is human-gated; loops are unattended. When self-verification and speed
  conflict, the gate wins.
- Two verifiers: Claude /goal for fast convergence, Codex for cross-provider sign-off.
- No LLM-council step in the loop — Codex review already covers the cross-model check.
- The opening mantra is intentional; kept short so it doesn't dilute the instructions.
