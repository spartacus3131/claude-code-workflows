# Claude Code Workflows

A practical guide to getting more out of Claude Code, based on [10 Tips from Inside the Claude Code Team](https://paddo.dev/blog/claude-code-team-tips/) and my own workflows.

**If you're not getting the output you want from Claude Code, or you've never used it before, this guide shows what's working well for me.**

---

## The 10 Tips (Quick Reference)

| # | Tip | My Implementation |
|---|-----|-------------------|
| 1 | **Parallelization** - Run 3-5 git worktrees with separate Claude sessions | [`/parallel-session`](skills/parallel-session/) |
| 2 | **Re-plan when stuck** - Use Plan Mode not just at start but when progress stalls | [Walkthrough: Plan Mode Recovery](walkthroughs/plan-mode-recovery.md) |
| 3 | **Claude writes its own rules** - "Update your CLAUDE.md so you don't make that mistake again" | [`/meta-rule`](skills/meta-rule/) |
| 4 | **Skills as institutional knowledge** - Convert workflows into reusable skills | All skills in this repo |
| 5 | **Claude fixes its own bugs** - Give access to logs and let it debug | [Walkthrough: Debug Mode](walkthroughs/debug-mode.md) |
| 6 | **Prompting as provocation** - Challenge Claude rather than instruct | [`/grill`](skills/grill/), [`/elegant-redo`](skills/elegant-redo/) |
| 7 | **Terminal setup matters** - Ghostty, color-coded tabs, voice dictation | [Setup Guide](setup.md) |
| 8 | **Subagents for context hygiene** - Offload tasks to preserve context | [Walkthrough: Subagents](walkthroughs/subagents.md) |
| 9 | **Claude replaces SQL** - Integrate DB CLIs into skills | Coming soon |
| 10 | **Learning with Claude** - Explanations, diagrams, presentations | [Walkthrough: Learning Mode](walkthroughs/learning-mode.md) |

---

## Getting Started

Copy the skills you want into your Claude config:

```bash
# Copy all skills
cp -r skills/* ~/.claude/skills/

# Or copy specific ones
cp -r skills/grill ~/.claude/skills/
```

Then invoke them with `/` commands in Claude Code.

---

## Skills

| Skill | What it does |
|-------|-------------|
| [`/grill`](skills/grill/SKILL.md) | Quiz yourself on changes before making a PR. Claude acts as a senior engineer and you don't ship until you pass. |
| [`/elegant-redo`](skills/elegant-redo/SKILL.md) | Scrap a messy implementation and redesign from scratch with everything you've learned. |
| [`/meta-rule`](skills/meta-rule/SKILL.md) | After a correction, Claude writes a rule for itself in CLAUDE.md so the mistake doesn't happen again. |
| [`/parallel-session`](skills/parallel-session/SKILL.md) | Analyze your backlog and recommend how to split work across parallel git worktrees. |
| [`/techdebt`](skills/techdebt/SKILL.md) | Scan the codebase for duplicated code, inconsistencies, and technical debt. |
| [`/vibe-pm`](skills/vibe-pm/SKILL.md) | Turn a vague feature idea into an unambiguous spec. Makes smart inferences instead of asking questions. |

---

## Walkthroughs

- [Plan Mode Recovery](walkthroughs/plan-mode-recovery.md) - What to do when you're stuck
- [Debug Mode](walkthroughs/debug-mode.md) - Letting Claude fix its own bugs
- [Subagents](walkthroughs/subagents.md) - Preserving context with task delegation
- [Learning Mode](walkthroughs/learning-mode.md) - Using Claude to understand unfamiliar code

---

## The Meta-Pattern

1. **Parallelize over optimize** - Run multiple sessions instead of perfecting one
2. **Plan Mode as recovery** - Not just for starting, but for getting unstuck
3. **Claude improves itself** - Let it write rules for its own mistakes
4. **Codify everything** - If you do it twice, make it a skill
5. **Challenge, don't command** - Provocation gets better results than instruction

---

## Contributing

Found a workflow that works well? Open a PR with:
- A new skill in `skills/`
- A walkthrough in `walkthroughs/`

---

## Sources

- [10 Tips from Inside the Claude Code Team](https://paddo.dev/blog/claude-code-team-tips/) - Boris Cherny
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
