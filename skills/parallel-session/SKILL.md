---
name: parallel-session
description: "Analyze work for parallelization. Checks feature lists, issues, and PRs to suggest whether to split work across multiple branches."
---

# Parallel Session Analyzer

At session start, analyze available work and recommend whether to parallelize across multiple git worktrees with separate Claude sessions.

## Steps

1. **Gather work items.** Check for feature lists, `gh issue list --state open`, open PRs, in-progress branches, and CLAUDE.md current focus.

2. **Categorize by independence.** Group items by whether they touch the same files/systems. Items touching the same component are sequential. Items in different domains can parallelize.

3. **Recommend branches.** If 3+ independent work streams exist, suggest named branches with specific items assigned to each. If work is sequential, say so and recommend an order. Include git worktree setup commands.

4. **Offer to set up.** Provide the exact `git worktree add` commands and explain how to run separate Claude sessions in each.
