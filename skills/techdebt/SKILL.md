---
name: techdebt
description: "Find technical debt, duplicated code, and inconsistencies. Use when you want a code quality audit. Invoke with '/techdebt' or 'find tech debt'."
---

# Tech Debt Finder

## Purpose

Scan the codebase for technical debt patterns and report findings with severity and suggested fixes.

## When to Trigger

- User says `/techdebt` or "find tech debt"
- User asks "what should I clean up?"
- User asks "are there any code smells?"

## What to Look For

### High Severity (Fix Soon)

| Pattern | How to Find | Why It Matters |
|---------|-------------|----------------|
| Duplicated logic | Functions with >70% similar code | Bug fixes need N changes |
| Dead code | Unused exports, unreachable branches | Confusion, bloat |
| Hardcoded secrets | API keys, passwords in code | Security risk |
| TODO/FIXME/HACK | Comments with these keywords | Known problems |
| Console.log in production | `console.log` outside dev utils | Noise, potential leaks |

### Medium Severity (Plan to Fix)

| Pattern | How to Find | Why It Matters |
|---------|-------------|----------------|
| Inconsistent naming | `getUserData` vs `fetchUser` | Cognitive load |
| Mixed async patterns | Callbacks + promises + async/await | Hard to maintain |
| Large files | >400 lines | Hard to navigate |
| Deep nesting | >4 levels of indentation | Hard to read |
| Implicit any (TS) | `any` types, missing types | Type safety gaps |

### Low Severity (Nice to Have)

| Pattern | How to Find | Why It Matters |
|---------|-------------|----------------|
| Old dependencies | `npm outdated` | Security, features |
| Missing tests | Low coverage areas | Regression risk |
| Inconsistent formatting | Mixed styles | Nitpicky PRs |

## Workflow

### Step 1: Quick Scan

Run these searches:

```bash
# TODOs and FIXMEs
grep -r "TODO\|FIXME\|HACK\|XXX" --include="*.ts" --include="*.tsx" --include="*.js"

# Console logs
grep -r "console.log" --include="*.ts" --include="*.tsx" --include="*.js" | grep -v "test\|spec\|.test.\|.spec."

# Hardcoded strings that might be secrets
grep -r "sk-\|api_key\|password\|secret" --include="*.ts" --include="*.tsx" --include="*.js" | grep -v ".env\|.example"
```

### Step 2: Structural Analysis

- List files by size (find large files)
- Check for duplicate function names across files
- Look for inconsistent patterns (e.g., some hooks use `useState`, others use Zustand)

### Step 3: Report

Output in this format:

```markdown
## Tech Debt Report

**Scan date**: [date]
**Files scanned**: [count]

### High Severity (X items)

#### 1. [Issue Title]
- **Location**: `path/to/file.ts:42`
- **Pattern**: [what's wrong]
- **Impact**: [why it matters]
- **Suggested fix**: [how to fix]

### Medium Severity (X items)
[Same format]

### Low Severity (X items)
[Same format]

### Summary

| Severity | Count | Estimated Effort |
|----------|-------|------------------|
| High | X | [time] |
| Medium | X | [time] |
| Low | X | [time] |

### Recommended Priority

1. [First thing to fix]
2. [Second thing to fix]
3. [Third thing to fix]
```

## Anti-Patterns

- Don't report style issues if there's a linter configured
- Don't flag test files for console.logs
- Don't recommend rewriting working code just for aesthetics
- Don't spend more than 2-3 minutes on the scan

## Example

**User**: `/techdebt`

**Claude**:

```markdown
## Tech Debt Report

**Scan date**: 2026-02-01
**Files scanned**: 47

### High Severity (3 items)

#### 1. Hardcoded API endpoint
- **Location**: `src/lib/api.ts:12`
- **Pattern**: `const API_URL = "https://api.example.com"`
- **Impact**: Can't switch environments without code change
- **Suggested fix**: Move to environment variable

#### 2. Duplicate form validation
- **Location**: `src/components/LoginForm.tsx` and `src/components/SignupForm.tsx`
- **Pattern**: Same email/password validation logic copied
- **Impact**: Bug fixes need two changes
- **Suggested fix**: Extract to `src/lib/validation.ts`

### Medium Severity (5 items)
[...]

### Summary

| Severity | Count | Estimated Effort |
|----------|-------|------------------|
| High | 3 | 1 hour |
| Medium | 5 | 2 hours |
| Low | 8 | 3 hours |
```
