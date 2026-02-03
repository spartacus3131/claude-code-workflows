---
name: parallel-session
description: "Session startup that analyzes work for parallelization. Checks feature lists, bug backlogs, and PRs to suggest whether to split work across multiple branches/agents. Invoke with 'analyze work for parallel' or 'should I parallelize this?'"
---

# Parallel Session Analyzer

## Purpose

At session start, analyze available work and recommend whether to parallelize across multiple branches/agents.

## When to Trigger

- User says "analyze work for parallel" or "should I parallelize?"
- User says "fire up the pod" (enhanced startup)
- User shares a feature list or backlog
- User asks "how should I tackle these X features?"

## Workflow

### Step 1: Gather Work Items

Look for work sources in this order:

1. **Feature list file** (if exists): `**/feature_list.json`, `**/features.json`, `**/TODO.md`
2. **GitHub issues**: `gh issue list --state open --limit 20`
3. **GitHub PRs needing review**: `gh pr list --state open`
4. **Git branches in progress**: `git branch -a`
5. **CLAUDE.md "Current Focus" section**

### Step 2: Categorize by Independence

Group work items by whether they touch the same files/systems:

| Category | Criteria | Can Parallelize? |
|----------|----------|------------------|
| Same component | Touch same files | No - sequential |
| Same domain | Related features, different files | Maybe - careful |
| Independent | Different domains entirely | Yes - parallelize |

### Step 3: Recommend Branches

**If 3+ independent work streams exist**, suggest:

```
Recommended parallel branches:

Branch A: [name] (worktree: ../project-a)
- Feature X
- Feature Y
- Bug Z

Branch B: [name] (worktree: ../project-b)
- Feature P
- Feature Q

Branch C: [name] (worktree: ../project-c)
- Feature M
- Feature N
```

**If work is sequential**, say so:
```
These items have dependencies. Recommend working sequentially on main.
Order: [item] → [item] → [item]
```

### Step 4: Offer to Set Up

If parallelization recommended:

```
Want me to set up worktrees? This will:
1. Create branch [name] and worktree at ../[project]-[name]
2. Create branch [name] and worktree at ../[project]-[name]

You can then run separate Claude sessions in each.
Commands to hop between them:
  cd ../[project]-[name]  # Branch A
  cd ../[project]-[name]  # Branch B
```

## Output Format

Always output:

```markdown
## Work Analysis

**Sources checked**: [list]
**Total items**: X features, Y bugs, Z PRs

## Parallelization Assessment

[Either "Recommend parallel" or "Recommend sequential"]

### Grouping

[Table or list of how items group]

### Recommendation

[Specific branches and what goes in each]

### Setup Commands (if parallel)

[Git worktree commands]
```

## Anti-Patterns

- Don't suggest parallelizing 2 items (not worth the overhead)
- Don't parallelize items that touch the same database tables
- Don't parallelize if user is unfamiliar with worktrees (offer to explain first)
- Don't create worktrees without user confirmation

## Example

**User**: "I have 14 features to build. Should I parallelize?"

**Claude**:

```markdown
## Work Analysis

**Sources checked**: feature_list.json
**Total items**: 14 features (0 complete in this batch)

## Parallelization Assessment

**Recommend parallel** - 3 independent workstreams detected

### Grouping

| Branch | Features | Domain | Files Affected |
|--------|----------|--------|----------------|
| challenge-ui | F018, F019, F020 | Challenge screens | src/app/challenges/, src/components/challenges/ |
| challenge-content | F021-F025 | Individual challenges + energy | src/modules/challenges/, src/modules/energy/ |
| infra-polish | F026-F031 | Utilities | src/hooks/, src/components/ui/ |

### Setup Commands

git worktree add ../claru-challenge-ui challenge-ui
git worktree add ../claru-challenge-content challenge-content
git worktree add ../claru-infra-polish infra-polish

Then open 3 terminals and run `claude` in each.
```
