---
name: meta-rule
description: "Update CLAUDE.md with a new rule after a correction. Use when Claude makes a mistake you don't want repeated. Invoke with '/meta-rule' or 'update your rules so you don't do that again'."
---

# Meta-Rule: Self-Improving Rules

## Purpose

When you correct Claude, this skill captures the lesson as a rule in CLAUDE.md so the mistake doesn't happen again.

## When to Trigger

- User says `/meta-rule` or "add that to your rules"
- User says "don't do that again"
- User says "remember this for next time"
- User says "update your CLAUDE.md"
- After any correction where the user seems frustrated

## Philosophy

Claude is eerily good at writing rules for itself. The best rules come from:
1. A specific mistake
2. Understanding why it was wrong
3. Generalizing to a pattern
4. Writing a clear, actionable rule

## Workflow

### Step 1: Identify the Mistake

Ask (or infer):
- What did I do wrong?
- Why was it wrong in this context?
- Is this specific to this project or general?

### Step 2: Generalize the Pattern

Transform the specific mistake into a general rule:

| Specific Mistake | General Rule |
|------------------|--------------|
| "You used `any` type in that function" | "Always use explicit types, never `any`" |
| "You created a new file when you should have edited" | "Prefer editing existing files over creating new ones" |
| "You wrote tests before I asked" | "Only write tests when explicitly requested" |
| "You used emojis in the commit message" | "No emojis in commit messages" |

### Step 3: Write the Rule

Rules should be:
- **Specific**: Not "write good code" but "use early returns instead of nested ifs"
- **Actionable**: Claude can follow it without judgment calls
- **Scoped**: Clear when it applies

Format:
```markdown
### [Category]

- [Rule statement]
  - Context: [when this applies]
  - Example: [what to do instead]
```

### Step 4: Find the Right Place

Check for existing CLAUDE.md:
1. Project root: `./CLAUDE.md`
2. Parent directory: `../CLAUDE.md`
3. User home: `~/.claude/CLAUDE.md` (global rules)

If project-specific, add to project CLAUDE.md.
If general, suggest adding to global.

### Step 5: Add the Rule

Open CLAUDE.md, find the appropriate section (or create one), and add the rule.

If no "Working Instructions" or "Rules" section exists, create:

```markdown
## AI Working Rules

### [Category]

- [Rule]
```

### Step 6: Confirm

```markdown
Added to CLAUDE.md:

> [The rule you added]

This will apply to future sessions in this project.
```

## Rule Categories

Use these categories to organize rules:

| Category | Examples |
|----------|----------|
| **Code Style** | Type annotations, naming conventions, formatting |
| **Architecture** | File organization, patterns to use/avoid |
| **Workflow** | When to commit, how to test, PR process |
| **Communication** | Tone, emoji usage, explanation depth |
| **Project-Specific** | Domain terms, business rules |

## Example

**User**: "You just used `console.log` for debugging. Don't do that, use the logger."

**Claude**: "Got it. Let me add that to the rules."

```markdown
I'll add this to CLAUDE.md:

### Code Style

- Use the project logger (`src/lib/logger.ts`) instead of `console.log`
  - Context: All logging in production code
  - Example: `logger.debug('user action', { userId })` not `console.log('user action', userId)`

Added to: ./CLAUDE.md under "AI Working Rules"

This will apply to all future sessions in this project.
```

## Anti-Patterns

- Don't add rules for one-off situations
- Don't add rules that contradict existing rules
- Don't add vague rules ("be careful with X")
- Don't add rules the user explicitly said they don't want
- Don't duplicate rules that already exist

## Proactive Usage

If you notice yourself making the same type of mistake twice, proactively suggest:

```markdown
I notice I've [done X] twice now and you've corrected me both times. Want me to add a rule for this?
```
