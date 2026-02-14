---
name: techdebt
description: "Find technical debt, duplicated code, and inconsistencies. Use when you want a code quality audit."
---

# Tech Debt Finder

Scan the codebase for technical debt and report findings with severity and suggested fixes.

## Steps

1. **Quick scan.** Search for TODOs/FIXMEs/HACKs, console.logs in production code, hardcoded secrets or API keys, and unused exports.

2. **Structural analysis.** Identify large files (>400 lines), duplicated logic across files, inconsistent naming or patterns, deep nesting, and missing types.

3. **Report findings** grouped by severity (high/medium/low). For each item include: location, what's wrong, why it matters, and suggested fix.

4. **Prioritize.** Recommend the top 3 things to fix first based on impact and effort.
