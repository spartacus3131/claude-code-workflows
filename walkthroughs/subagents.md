# Subagents for Context Hygiene

**Tip #8**: Offload discrete tasks to subagents to preserve your main agent's context window.

---

## The Problem

Long Claude sessions accumulate context:
- Previous code you looked at
- Conversations about approaches you didn't take
- Errors that got fixed
- Research that's no longer relevant

This fills the context window and degrades performance.

---

## The Technique

Use subagents for discrete, self-contained tasks. The main agent stays focused.

```
> Use a subagent to research the authentication options.
> Report back with a recommendation.
```

The subagent:
- Gets a fresh context window
- Does the work
- Returns a summary
- Context doesn't pollute your main session

---

## When to Use Subagents

| Task Type | Use Subagent? | Why |
|-----------|---------------|-----|
| Research a library | Yes | Lots of docs to read, only need the conclusion |
| Explore unfamiliar code | Yes | Exploration generates noise |
| Run tests and analyze | Yes | Test output is verbose |
| Write a single function | No | Too small, overhead not worth it |
| Refactor a file | No | Need the main context |
| Debug with logs | Maybe | Depends on log volume |

---

## Example: Library Research

**Without subagent** (pollutes context):
```
> What's the best date library for this project?
```
Claude reads 5 library docs, compares them, fills context with details you don't need.

**With subagent** (clean context):
```
> Use a subagent to research date libraries for React.
> Requirements: timezone support, bundle size <10kb, TypeScript.
> Report back with top 2 options and a recommendation.
```

Main context only gets the summary:
```
## Date Library Research (Subagent)

**Recommendation**: date-fns
- 8kb bundle (tree-shakeable)
- Full timezone support via date-fns-tz
- Excellent TypeScript types

**Runner-up**: dayjs (6kb but weaker timezone handling)
```

---

## Example: Test Analysis

```
> Use a subagent to run the test suite and analyze failures.
> Only report back tests that failed and why.
```

The subagent runs 200 tests, sees 195 pass, analyzes the 5 failures, and returns:

```
## Test Failures (5 of 200)

1. `checkout.test.ts:45` - Timeout on payment mock
   - Fix: Increase mock timeout or fix async handling

2. `auth.test.ts:112` - Token expired assertion
   - Fix: Test uses real time, needs fake timers

[3 more...]
```

Your main context stays clean.

---

## Subagent Prompts That Work

### Research Task
```
> Use a subagent to research [topic].
> Answer these specific questions: [list]
> Report back in <100 words.
```

### Exploration Task
```
> Use a subagent to explore how [feature] works in this codebase.
> I need to understand: [specific question]
> Summarize the key files and flow.
```

### Analysis Task
```
> Use a subagent to analyze [logs/output/data].
> Look for: [pattern]
> Report anomalies only.
```

---

## Pro Tips

### 1. Be Specific About Output Format

```
> Report back as a bullet list, max 5 items.
```

### 2. Set Scope Limits

```
> Spend no more than 10 minutes on this.
> If you can't find the answer, report what you learned.
```

### 3. Use for Permission Checks

The Claude Code team routes security-sensitive operations to more capable models as gates:

```
> Use a subagent to verify this database migration is safe.
> Check for: data loss, downtime, rollback issues.
```

---

## Anti-Patterns

- **Don't use subagents for tiny tasks** - The overhead isn't worth it
- **Don't lose the summary** - Make sure findings come back to main session
- **Don't chain too many subagents** - Gets confusing, just start fresh
- **Don't forget to specify output format** - Verbose summaries defeat the purpose
