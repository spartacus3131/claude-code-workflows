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

### 1. Install the Skills

Copy the skills you want into your Claude config:

```bash
# Copy all skills
cp -r skills/* ~/.claude/skills/

# Or copy specific ones
cp -r skills/grill ~/.claude/skills/
cp -r skills/meta-rule ~/.claude/skills/
```

### 2. Use Them

Skills are invoked with `/` commands:

```
/grill          # Quiz yourself before a PR
/elegant-redo   # Scrap and rewrite elegantly
/meta-rule      # Add a rule after a correction
/parallel-session  # Analyze work for parallelization
/techdebt       # Find code quality issues
/vibe-pm        # Generate specs from vague ideas
```

---

## Skills Overview

### `/grill` - Pre-PR Quiz

**Problem**: You're about to open a PR but you're not sure you understand all your changes.

**Solution**: Claude acts as a senior engineer and quizzes you. You don't make a PR until you pass.

```
> /grill

## Grill Session

I've reviewed your changes to the checkout flow. 3 files changed, 127 additions.

**Question 1 of 5**

You added retry logic for payment failures. What happens if all 3 retries fail?
Walk me through the user experience.
```

[Full skill documentation](skills/grill/SKILL.md)

---

### `/elegant-redo` - Clean Rewrite

**Problem**: Your code works but it's messy. You know there's a better way.

**Solution**: Claude analyzes what's wrong, proposes an elegant approach, and rewrites from scratch.

```
> This checkout flow works but it's a mess. /elegant-redo

## Current Implementation Analysis

**Core problem**: Process a multi-step checkout
**What's wrong**: State scattered across 4 useState hooks + localStorage
**The insight**: This is a state machine. Use XState.

This would replace 280 lines across 4 files with 120 lines in 2 files.
Should I proceed?
```

[Full skill documentation](skills/elegant-redo/SKILL.md)

---

### `/meta-rule` - Self-Improving Rules

**Problem**: Claude keeps making the same mistake.

**Solution**: After a correction, Claude writes a rule for itself in CLAUDE.md.

```
> You just used console.log for debugging. Don't do that, use the logger.
> /meta-rule

Added to CLAUDE.md:

### Code Style
- Use the project logger (`src/lib/logger.ts`) instead of `console.log`
  - Context: All logging in production code
  - Example: `logger.debug('user action', { userId })`

This will apply to all future sessions in this project.
```

[Full skill documentation](skills/meta-rule/SKILL.md)

---

### `/parallel-session` - Work Parallelization

**Problem**: You have 10 features to build. Should you work on them one at a time?

**Solution**: Claude analyzes your backlog and recommends how to split work across parallel branches.

```
> I have 14 features to build. /parallel-session

## Parallelization Assessment

**Recommend parallel** - 3 independent workstreams detected

| Branch | Features | Domain |
|--------|----------|--------|
| auth-flow | F01, F02, F03 | Authentication |
| dashboard | F04-F08 | Dashboard UI |
| api-layer | F09-F14 | API endpoints |

Setup commands:
git worktree add ../project-auth auth-flow
git worktree add ../project-dashboard dashboard
git worktree add ../project-api api-layer
```

[Full skill documentation](skills/parallel-session/SKILL.md)

---

### `/techdebt` - Code Quality Audit

**Problem**: You suspect there's duplicated code, inconsistencies, or technical debt.

**Solution**: Claude scans the codebase and reports issues.

[Full skill documentation](skills/techdebt/SKILL.md)

---

### `/vibe-pm` - Spec Generation

**Problem**: You have a vague feature idea ("add a logout button") and Claude keeps asking clarifying questions.

**Solution**: Claude makes smart inferences and generates an unambiguous spec instead of asking.

```
> Add a logout button

## Logout Button

**Outcome**: Users can log out from any page with one click.

**Requirements**:
- Logout button in header, right side
- Clears auth token from localStorage and context
- Redirects to /login

**Assumptions Made**:
- Using existing IconButton component (matches Header.tsx pattern)
- Logout API endpoint is POST /api/auth/logout
```

[Full skill documentation](skills/vibe-pm/SKILL.md)

---

## Walkthroughs

Step-by-step guides for specific workflows:

- [Plan Mode Recovery](walkthroughs/plan-mode-recovery.md) - What to do when you're stuck
- [Debug Mode](walkthroughs/debug-mode.md) - Letting Claude fix its own bugs
- [Subagents](walkthroughs/subagents.md) - Preserving context with task delegation
- [Learning Mode](walkthroughs/learning-mode.md) - Using Claude to understand unfamiliar code

---

## The Meta-Pattern

These techniques share common principles:

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
- An example in `examples/`

---

## Sources

- [10 Tips from Inside the Claude Code Team](https://paddo.dev/blog/claude-code-team-tips/) - Boris Cherny
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
