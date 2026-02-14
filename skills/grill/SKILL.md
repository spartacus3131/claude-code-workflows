---
name: grill
description: "Quiz yourself on changes before making a PR. Claude acts as a senior engineer challenging your understanding."
---

# Grill: Pre-PR Quiz

Before you ship, Claude quizzes you on your changes. You don't make a PR until you pass.

## Steps

1. **Review the changes.** Run `git diff main...HEAD` and `git log main..HEAD --oneline` to understand the full scope.

2. **Generate 5-7 questions** across these categories: why you made each decision, edge cases, failure modes, scale implications, security, testing gaps, and trade-offs.

3. **Present one question at a time.** Wait for the user's answer before moving on. If the answer is partial, probe deeper with a follow-up.

4. **After all questions, give a verdict.** Either "Ready to Ship" or "Needs Work" with specific strengths, areas to address, and a concrete recommendation.
