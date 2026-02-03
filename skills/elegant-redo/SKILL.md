---
name: elegant-redo
description: "Scrap the current approach and implement the elegant solution. Use after a mediocre first attempt when you know there's a better way. Invoke with '/elegant-redo' or 'knowing what you know now, redo this elegantly'."
---

# Elegant Redo

## Purpose

After a working but messy implementation, step back and redesign from scratch with everything you've learned. The goal is the solution you'd be proud to show a senior engineer.

## When to Trigger

- User says `/elegant-redo` or "redo this elegantly"
- User says "knowing what you know now..."
- User says "this works but it's ugly"
- User says "there has to be a better way"

## Philosophy

First implementations often accumulate cruft:
- Workarounds for problems you didn't anticipate
- Abstractions that don't quite fit
- Copy-pasted code that should be unified
- Error handling bolted on after the fact

An elegant redo isn't about making it "clever." It's about making it:
- **Obvious**: A new engineer can read it and understand
- **Minimal**: Nothing unnecessary
- **Robust**: Handles errors gracefully, not as afterthoughts
- **Idiomatic**: Uses the language/framework the way it's meant to be used

## Workflow

### Step 1: Identify What Exists

```bash
git diff main...HEAD --stat  # See scope of changes
```

Read the current implementation without judgment. Note:
- What problem is this solving?
- What are the inputs and outputs?
- What are the edge cases handled?
- What's the messy part?

### Step 2: Extract the Essence

Before rewriting, articulate:

```markdown
## Current Implementation Analysis

**Core problem**: [one sentence]

**What it must do**:
- [requirement 1]
- [requirement 2]

**What's wrong with current approach**:
- [issue 1]
- [issue 2]

**The insight for a better approach**:
[The key realization that enables elegance]
```

### Step 3: Propose the Elegant Approach

Don't just start coding. First, describe:

```markdown
## Elegant Approach

**Key insight**: [the conceptual shift]

**New structure**:
[Describe the new approach in 3-5 sentences]

**Why this is better**:
- [benefit 1]
- [benefit 2]

**Trade-offs**:
- [what we give up, if anything]
```

### Step 4: Get Confirmation

Ask the user:

```markdown
This approach would replace [X lines] with [Y lines] and eliminate [specific problems].

Should I proceed with the rewrite?
```

### Step 5: Implement

When implementing:
- Delete the old code (don't comment it out)
- Write tests first if they don't exist
- Use the language idiomatically
- Handle errors as part of the design, not bolted on
- Name things clearly

### Step 6: Compare

After implementing, show:

```markdown
## Before vs After

| Metric | Before | After |
|--------|--------|-------|
| Lines of code | X | Y |
| Files touched | X | Y |
| Cyclomatic complexity | X | Y |
| Key improvement | - | [what's better] |

### The key change

[Explain the conceptual improvement in 2-3 sentences]
```

## Examples of Elegance

### Replace conditionals with polymorphism
**Before**: 50-line switch statement for different user types
**After**: UserType interface with 3 implementations

### Replace imperative with declarative
**Before**: 30 lines of array manipulation with loops
**After**: 5-line chain of filter/map/reduce

### Replace flags with state machine
**Before**: 5 boolean flags tracking status
**After**: Single enum with clear transitions

### Replace duplication with abstraction
**Before**: Same 20 lines in 4 places
**After**: One function called 4 times

## Anti-Patterns

- Don't over-engineer (elegance ≠ abstraction for its own sake)
- Don't use obscure language features to "show off"
- Don't sacrifice readability for brevity
- Don't rewrite working code without a clear improvement thesis
- Don't change APIs without considering downstream impact

## Example

**User**: "This checkout flow works but it's a mess. /elegant-redo"

**Claude**:

```markdown
## Current Implementation Analysis

**Core problem**: Process a multi-step checkout (cart → shipping → payment → confirm)

**What it must do**:
- Validate each step before proceeding
- Preserve state across steps
- Handle failures at any point
- Allow going back

**What's wrong with current approach**:
- State scattered across 4 useState hooks + localStorage
- Validation logic duplicated in each step component
- Error handling inconsistent (some throws, some returns null)
- Back navigation rebuilds state from localStorage (fragile)

**The insight for a better approach**:
This is a state machine. Each step is a state, transitions have guards (validation), and there's one source of truth.

## Elegant Approach

**Key insight**: Replace imperative state management with a checkout state machine using XState (already in dependencies).

**New structure**:
- Single `checkoutMachine` with states: cart, shipping, payment, confirm, complete, error
- Each transition validates via guards
- Context holds all checkout data
- Components just render current state and send events

**Why this is better**:
- Impossible to be in invalid states
- Back/forward handled by machine
- Error handling is a state, not scattered try/catches
- Easy to add analytics (just observe transitions)

**Trade-offs**:
- Team needs to understand XState (30-min learning curve)

This would replace 280 lines across 4 files with 120 lines in 2 files.

Should I proceed with the rewrite?
```
