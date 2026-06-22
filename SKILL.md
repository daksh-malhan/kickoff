---
name: kickoff
description: >
  Full project-kickoff workflow: grill-me-codex hardens the idea into clustered
  mini-goals, a human approval gate, per-cluster self-driven build loops with
  Codex sign-off, then a ponytail-audit gate and final Codex verification. Runs
  on an Obsidian second-brain for cross-session memory. Invoke manually with
  /kickoff at the start of a new project.
disable-model-invocation: true
user-invocable: true
---

# Project Kickoff

> Assumes installed: `grill-me-codex` (chaseai-yt) and an authenticated Codex
> CLI (>= 0.130). If any are missing, STOP and tell me.

The project idea is provided in $ARGUMENTS. If $ARGUMENTS is empty, ask me for
the idea before doing anything else.

## Phase 1 — Caveman on (token discipline)

Before anything else, activate the `caveman` skill set for the whole session:
- Invoke `/caveman full` so all chat for this project runs token-compressed.
- Use `/caveman-commit` for every commit and `/caveman-review` for every review.
- Persist it: append a line to the project `CLAUDE.md` — "Kickoff: caveman full
  active for this repo; commits via caveman-commit, reviews via caveman-review" —
  so future Claude sessions in this repo keep the same discipline.
- Caveman's own boundaries still hold: CODE, COMMITS, PRs, and SECURITY notes are
  written normal — only the surrounding chat is compressed. Don't compress code.

## Phase 2 — Second brain (Obsidian vault = long-term memory)

Auto-create a per-project Obsidian vault, path DERIVED from the project root:
`<project-root>/brain/` (use the working directory; `mkdir -p` the subfolders if
absent). This is durable cross-session memory so chat context isn't exhausted — the
vault holds the detail, chat holds pointers.

RESUME FIRST: at kickoff start, if `brain/` already exists, READ `brain/index.md`
and follow its links to restore state BEFORE doing anything else. Don't re-derive
what's already captured — this is the whole point of multi-session memory.

Layout (all markdown, `[[wikilinks]]` so the Obsidian graph connects them):
- `brain/index.md` — MEMORY-style index: one line + `[[link]]` per note. Loaded
  first; links the canonical root `PLAN.md` directly.
- `brain/decisions/<slug>.md` — one key decision each, with the why.
- `brain/clusters/<n>-<name>.md` — per-cluster progress log: status, what was built,
  verify evidence (Codex verdict, test/lint).
- `brain/context/<topic>.md` — reusable context snapshots: codebase map, env, gotchas.

Rules:
- POINTERS not dumps: in chat say `see [[cluster-2-progress]]`; read a note on
  demand, never hold all notes in context at once.
- WRITE-AS-YOU-GO: every resolved decision, every cluster's progress + verify
  evidence, every reusable fact → its vault note. Update `index.md` each time.
- `/caveman-compress` the brain notes to save tokens (readable `*.original.md` kept).

## Phase 3 — Align (human in the loop)

Run `/grill-me-codex` on the idea.

- Act 1: Interview me relentlessly, ONE question at a time, depth-first down the
  decision tree. For each question give your recommended answer plus a one-line
  rationale. Explore the codebase first for anything the code can already answer.
- Before exiting Act 1, list every key decision and have me confirm each one
  explicitly. Nothing implicit. Write each confirmed decision to
  `brain/decisions/<slug>.md` (with the why) and link it from `brain/index.md`.
- The plan is not done until each mini-goal has measurable, checkable success
  criteria (tests pass, lint clean, specific observable behavior). Vague = not done.
- Bias the plan toward small, compartmentalized, independently-verifiable
  mini-goals (vertical slices). Each shippable and checkable on its own.
- Group the mini-goals into clusters (small batches of related slices). The
  cluster is the unit of Codex review; clusters drive the build cadence.
- Act 2: Hand the plan to Codex for adversarial cross-model review. Iterate rounds
  until both models sign off. Codex stays read-only; no code yet.

Output `PLAN.md` (the what) and `PLAN-REVIEW-LOG.md` (the why).

GATE 1 — STOP. Show me PLAN.md. I approve it by hand before any code is written.
Do not proceed past this line without my explicit "approved."

After approval (not before — I read the readable version at the gate), compress the
log to save tokens on every re-read: `/caveman-compress PLAN-REVIEW-LOG.md`
(readable `*.original.md` backup kept). Leave `PLAN.md` UNCOMPRESSED — the build
loops and Codex read its success criteria verbatim; a lossy rewrite there risks the
whole build.

## Phase 4 — Build (unattended loop, by cluster)

Mini-goals in the approved PLAN.md are grouped into clusters (small batches of
related vertical slices). Work clusters in order. For each cluster:

1. Build each mini-goal in the cluster, in order. Run a self-driven loop per
   mini-goal. Open each iteration with this exact line, then the task:
   "Stay calm. You are loved. Do not panic, it's going to be alright."
   Set a max-iteration cap and an explicit completion condition (the mini-goal's
   success criteria). Stop when met or cap hit.
   For any mini-goal that builds or reshapes UI, invoke `/frontend-design` first
   to set aesthetic direction (typography, layout, intentional non-default look),
   then build to it. Skip for non-UI mini-goals.
2. Codex signs off PER CLUSTER: once the cluster's mini-goals are built, open a
   clean `/codex` review scoped to ONLY this cluster's mini-goals and ONLY their
   criteria, fed verbatim. Gaps → pull Codex's concrete items back into the loops
   and repeat from step 1 for the affected mini-goals. Loop until Codex confirms.
3. Mark the cluster complete. Write/update `brain/clusters/<n>-<name>.md` — status,
   what was built, Codex verdict + test/lint evidence — and update `brain/index.md`.
   Keep chat referencing it by pointer. Move to the next cluster.

## Phase 5 — Final verification

After all mini-goals are done:
1. Full verification on the WHOLE project (build, full test suite, lint, end-to-end
   against the top-level success criteria). The single regression sweep — covers
   every mini-goal's criteria and cross-cluster breakage at once.
2. Over-engineering audit: run `/ponytail-audit` on the whole repo. It returns a
   ranked list of what to delete / simplify / replace with stdlib-native — no fixes
   applied.

   GATE 2 — STOP. Show me the FULL audit findings (each item: location, what to cut,
   what replaces it). Ask me which items to implement. Apply ONLY the ones I approve;
   skip the rest. Do not proceed to the final Codex review until I've chosen.
3. After my approved simplifications land, re-run step 1 (build/test/lint) so the
   cuts didn't break anything.
4. FINAL Codex verification: open a clean `/codex` review (read-only) over the whole
   project, scoped to the top-level success criteria, fed verbatim. Gaps → fix and
   re-review the SAME session until `VERDICT: APPROVED`.
5. Report a checklist: each criterion, pass/fail, evidence. Anything not green goes
   back into a scoped loop. Save the final checklist to `brain/context/final-verification.md`
   and link from `brain/index.md`.

## Deliberate choices
(Only the non-obvious ones — each phase justifies itself inline.)
- Plan is human-gated; loops are unattended. When self-verification and speed
  conflict, the gate wins.
- Ponytail-audit runs BEFORE the final Codex review, not after: strip
  over-engineering (human-gated) so Codex signs off on the code you keep, not
  pre-trimmed bloat.
- The opening mantra is intentional; kept short so it doesn't dilute the instructions.
