---
name: meta-rule
description: "Update CLAUDE.md with a new rule after a correction. Use when Claude makes a mistake you don't want repeated."
---

# Meta-Rule: Self-Improving Rules

When you correct Claude, this skill captures the lesson as a rule in CLAUDE.md so the mistake doesn't happen again.

## Steps

1. **Identify the mistake.** What went wrong, why it was wrong in this context, and whether it's project-specific or general.

2. **Generalize to a pattern.** Transform the specific mistake into an actionable rule with a clear scope. Rules should be specific enough that Claude can follow them without judgment calls.

3. **Write the rule** in this format and add it to the appropriate CLAUDE.md (project root for project-specific, `~/.claude/CLAUDE.md` for global):

```markdown
### [Category]
- [Rule statement]
  - Context: [when this applies]
  - Example: [what to do instead]
```

4. **Confirm what was added** and where. If you notice the same mistake happening twice, proactively suggest adding a rule.
