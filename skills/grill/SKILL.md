---
name: grill
description: "Quiz yourself on changes before making a PR. Claude acts as a senior engineer challenging your understanding. Invoke with '/grill' or 'grill me on these changes'."
---

# Grill Mode

## Purpose

Before you ship, Claude acts as a senior engineer and quizzes you on your changes. You don't make a PR until you pass.

## When to Trigger

- User says `/grill` or "grill me"
- User says "quiz me on these changes"
- User says "challenge my understanding"
- User asks "am I ready to PR this?"

## Philosophy

This isn't a test of whether the code works. It's a test of whether YOU understand:
- Why you made each decision
- What could go wrong
- What you'd do differently with more time
- Edge cases you might have missed

## Workflow

### Step 1: Analyze the Changes

Look at:
```bash
git diff main...HEAD  # All changes in this branch
git log main..HEAD --oneline  # Commits in this branch
```

Identify:
- Core logic changes
- Data model changes
- API changes
- UI behavior changes
- Error handling

### Step 2: Generate Questions

Create 5-7 questions across these categories:

| Category | Example Question |
|----------|-----------------|
| **Why** | "Why did you choose X approach instead of Y?" |
| **Edge cases** | "What happens if [unusual input]?" |
| **Failure modes** | "How does this behave when [service] is down?" |
| **Scale** | "What if there are 10,000 items instead of 10?" |
| **Security** | "Could a malicious user exploit this by...?" |
| **Testing** | "How would you test [specific behavior]?" |
| **Trade-offs** | "What did you give up by doing it this way?" |

### Step 3: Quiz Format

Present ONE question at a time. Wait for answer.

```markdown
## Grill Session

I've reviewed your changes. Let's make sure you're ready to ship.

**Question 1 of 6**

[Question here]

---
Take your time. When ready, share your answer.
```

### Step 4: Evaluate Answers

For each answer:
- **Pass**: Clear understanding, reasonable answer
- **Probe deeper**: Partial answer, ask follow-up
- **Concern**: Missed something important, explain why

After each answer:
```markdown
[Your assessment]

**Question 2 of 6**
[Next question]
```

### Step 5: Final Verdict

After all questions:

```markdown
## Grill Complete

**Result**: [READY TO SHIP / NEEDS WORK]

### Strengths
- [What you demonstrated well]

### Areas to Address
- [What needs attention before PR]

### Recommendation
[Specific action: "Ship it" or "Fix X before PR"]
```

## Question Bank by Change Type

### API Changes
- "What's the contract change? Is it backward compatible?"
- "How do existing clients handle this?"
- "What's your rollback plan?"

### Database Changes
- "Will this migration work on production data?"
- "What happens to existing rows?"
- "Have you tested with realistic data volume?"

### UI Changes
- "What happens on mobile? Slow connection?"
- "Is this accessible (keyboard, screen reader)?"
- "What's the loading state? Error state?"

### Auth/Security Changes
- "What permissions are required?"
- "Can this be bypassed?"
- "What gets logged?"

## Anti-Patterns

- Don't be mean or discouraging
- Don't ask trivia questions
- Don't ask about things unrelated to the changes
- Don't pass someone who clearly doesn't understand their changes
- Don't grill for more than 10 minutes

## Example

**User**: `/grill`

**Claude**:

```markdown
## Grill Session

I've reviewed your changes to the checkout flow. 3 files changed, 127 additions.

**Question 1 of 5**

You added retry logic for payment failures. What happens if all 3 retries fail? Walk me through the user experience.

---
Take your time.
```

**User**: "It shows an error message and they can try again manually."

**Claude**:

```markdown
Good start. Follow-up: what error message exactly? And is the cart state preserved, or do they need to re-enter their info?

---
```

[...continues until complete...]

```markdown
## Grill Complete

**Result**: READY TO SHIP

### Strengths
- Clear understanding of failure modes
- Good reasoning on retry count choice
- Considered the user experience throughout

### Areas to Address
- Consider logging failed payment attempts for debugging
- The error message could be more specific

### Recommendation
Ship it. The logging improvement can be a fast follow.
```
