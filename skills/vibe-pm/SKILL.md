---
name: vibe-pm
description: "Product thinking for vibe coding. Transforms vague feature ideas into unambiguous specs. Apply when user describes a feature to build, discusses product requirements, or asks 'how should this work?' Makes smart inferences instead of asking questions."
---

# Vibe PM: Product Thinking for LLM Coding

## Core Behavior

When the user describes a feature, **produce a spec and build it**. Don't ask clarifying questions unless absolutely necessary. Make smart inferences and document them.

**Goal**: User describes feature once → Claude produces spec → Claude saves it → Claude builds it.

**Persistence**: After producing a spec, write it to `/specs/YYYY-MM-DD-feature-slug.md` in the project root. Create the `/specs` folder if it doesn't exist. This creates a searchable history of what was built and when.

---

## 5 Principles

### 1. Lead with the Outcome
First sentence = what's true when this ships.

### 2. Capture the Job, Not the Feature
"When [situation], I want to [motivation], so I can [outcome]."

### 3. Define Boundaries First
State what we're NOT building before listing requirements.

### 4. Concrete Over Abstract
One specific example beats ten abstract rules.

### 5. Infer and State, Don't Ask
Make reasonable assumptions. Document them. Keep moving.

---

## When to Ask (Rarely)

Ask **only if**:
- Two completely different features could satisfy the request
- A wrong assumption would waste significant time (architectural, not UI)
- There's an obvious fork: "Do you want X or Y?"

**Maximum**: 2-3 questions per feature. Default: infer and state.

---

## Inference Strategy

| Ambiguity | How to Infer |
|-----------|--------------|
| UI placement | Look at existing patterns in codebase |
| Styling | Match existing design system |
| Error messages | Use tone from existing error handling |
| API structure | Follow existing endpoint patterns |
| State management | Use what the app already uses |
| File location | Follow existing directory structure |

**Always state**: "Assumption: [what] because [rationale from codebase]"

---

## Proportional Specs

### Tiny (< 30 min)
2-3 sentences. Just do it.

"Add logout button to header. Clears session, redirects to /login."

### Small (< 1 hour)

```markdown
## [Feature]

**Outcome**: [One sentence]

**Job**: When [situation], I want to [motivation], so I can [outcome].

**Requirements**:
- [Requirement]
- Edge case: [scenario] → [handling]

**Not Building**: [Exclusions]

**Assumptions**: [What you inferred and why]
```

### Medium (half-day to 2 days)

```markdown
## [Feature]

**Outcome**: [One sentence]

**Job**: When [situation], I want to [motivation], so I can [outcome].

---

### Requirements

**Happy Path**:
Given [context]
When [action]
Then [result]

**Edge Cases**:
| Condition | Behavior |
|-----------|----------|
| [case] | [handling] |

---

### States

| State | Display | Actions |
|-------|---------|---------|
| Loading | [what] | [what] |
| Empty | [what] | [what] |
| Populated | [what] | [what] |
| Error | [what] | [what] |

---

### Boundaries

**Not Building**: [list]

**Assumptions**: [list with rationale]

---

### Done When
- [ ] [Testable criterion]
```

### Big (3+ days)
Use medium template + break into chunks with build order.

---

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Ask about every edge case | Handle obvious ones, document assumptions |
| 500 words for a button | Match depth to size |
| "Fast", "intuitive" | "<200ms", "2 clicks" |
| Happy path only | Include 2-3 edge cases |
| Over-specify implementation | Define outcome, figure out how |

---

## Example

**User**: "Add a logout button"

**Claude produces**:

```markdown
## Logout Button

**Outcome**: Users can log out from any page with one click.

**Job**: When I'm done working, I want to end my session securely, so I can protect my account on shared devices.

**Requirements**:
- Logout button in header, right side (next to user avatar)
- Clicking clears auth token from localStorage and context
- Redirects to /login after logout
- Edge case: If logout API fails, still clear local state and redirect

**Not Building**: Confirmation modal, session timeout, "remember me"

**Assumptions**:
- Using existing IconButton component (matches Header.tsx)
- POST /api/auth/logout (matches login pattern)
- Auth state in AuthContext (src/contexts/AuthContext.tsx)

**Done When**:
- [ ] Button visible in header when logged in
- [ ] Clicking logs out and redirects to /login
- [ ] Auth state cleared from context and localStorage
```

Then Claude builds it.
