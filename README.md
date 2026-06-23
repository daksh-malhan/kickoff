# kickoff

A user-invocable Claude Code skill — `/kickoff` — that runs a full
project-kickoff workflow: it interrogates your idea until the plan is sound,
makes you approve it, then builds it cluster by cluster with cross-model review,
an over-engineering gate, and a final sign-off. See [SKILL.md](SKILL.md).

```bash
/kickoff <your project idea>
```

## What it does

Starting a project from a one-line idea usually means building the wrong thing,
or building the right thing badly. `kickoff` puts structure around both risks: a
human-gated planning stage, then an unattended build with layered verification.

### The five phases

| Phase | Name | What happens |
|-------|------|--------------|
| 1 | **Caveman on** | Activate token-compressed chat for the whole session. Code, commits, PRs, and security notes stay verbatim. |
| 2 | **Second brain** | Auto-create an Obsidian vault at `<project>/brain/` for cross-session memory. On restart, state is restored from `brain/index.md` instead of re-derived. |
| 3 | **Align** | Run `grill-me-codex`: Claude interviews you one question at a time and captures intent as `PRD.md` (problem, users, requirements, success metrics, scope). `PLAN.md` is then *derived* from the PRD — clustered mini-goals, each citing the requirement it serves, with measurable success criteria. Codex adversarially reviews both. **GATE 1**: you approve by hand before any code. |
| 4 | **Build** | For each cluster, a self-driven loop builds every mini-goal to its success criteria, then Codex signs off on the cluster. UI mini-goals run `frontend-design` first. |
| 5 | **Final verification** | One whole-project regression sweep → `ponytail-audit` over-engineering report (**GATE 2**: you choose what to cut) → re-verify → final Codex review until `VERDICT: APPROVED`. |

### Three real gates, not nine

Verification is layered, never repeated:

1. **The grill** hardens the plan before any code exists.
2. **Per-cluster Codex** sign-off catches problems as each slice lands.
3. **One Phase 5 sweep** covers every mini-goal's criteria and cross-cluster
   regressions at once.

No mid-build re-verification, no per-mini-goal re-checks — those were redundant
and were cut.

### Memory & cost

- **Obsidian second-brain** keeps detail on disk; chat holds pointers
  (`see [[cluster-2-progress]]`). This saves tokens across sessions and via
  on-demand reads — not within one continuous chat.
- **Caveman** compresses live chat and commit messages only — never stored
  artifacts. `PRD.md`, `PLAN.md`, the review log, and brain notes all stay
  verbatim, so nothing the build or Codex reads is ever a lossy rewrite. Token
  savings come from compressed chat plus the pointer discipline above.

## Install

```bash
mkdir -p ~/.claude/skills/kickoff
cp SKILL.md ~/.claude/skills/kickoff/SKILL.md
```

Then invoke with `/kickoff <your project idea>` at the start of a new project.

### Assumes installed

- [`grill-me-codex`](https://github.com/chaseai-yt) — the plan-hardening skill
- an authenticated Codex CLI (>= 0.130)
- the `caveman`, `ponytail`, and `frontend-design` skills — kickoff calls
  `/caveman`, `/caveman-commit`, `/ponytail-audit`, and `/frontend-design`
  during the run

If any are missing, the skill stops and tells you.

## License

MIT.
