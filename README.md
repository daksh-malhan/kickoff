# claude-code-kickoff

A user-invocable Claude Code skill: `/kickoff`.

Full project-kickoff workflow — `grill-me-codex` hardens the idea into clustered
mini-goals, a human approval gate, per-cluster self-driven build loops with Codex
sign-off, then a `ponytail-audit` gate and final Codex verification. Runs on an
Obsidian second-brain (`brain/`) for cross-session memory. See [SKILL.md](SKILL.md).

Five phases: caveman token discipline → second-brain setup → align (grill + gate)
→ build by cluster → final verification. Verification collapses to three real
gates: the grill, per-cluster Codex sign-off, and one whole-project Phase 5 sweep.

## Install

Copy the skill into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/kickoff
cp SKILL.md ~/.claude/skills/kickoff/SKILL.md
```

Then invoke with `/kickoff <your project idea>` at the start of a new project.

### Assumes installed
- `grill-me-codex` (chaseai-yt)
- an authenticated Codex CLI (>= 0.130)
- the `caveman`, `ponytail`, and `frontend-design` skills (kickoff calls
  `/caveman`, `/caveman-compress`, `/ponytail-audit`, `/frontend-design`
  during the run)
