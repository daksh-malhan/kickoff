# claude-code-kickoff

A user-invocable Claude Code skill: `/kickoff`.

Full project-kickoff workflow — `grill-me-codex` to harden the plan, a human
approval gate, then per-mini-goal Ralph loops with Claude `/goal` plus Codex
sign-off. See [SKILL.md](SKILL.md).

## Install

Copy the skill into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/kickoff
cp SKILL.md ~/.claude/skills/kickoff/SKILL.md
```

Then invoke with `/kickoff <your project idea>` at the start of a new project.

### Assumes installed
- `grill-me-codex` (chaseai-yt)
- the `ralph-loop` plugin
- an authenticated Codex CLI (>= 0.130)
