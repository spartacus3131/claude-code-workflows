# Plan Mode Recovery

**Tip #2**: Re-plan when stuck - Use Plan Mode not just at start but when progress stalls.

---

## The Problem

You're mid-task and things have gone sideways:
- The approach isn't working
- You've hit an unexpected blocker
- Claude is going in circles

Most people either push through or start over. There's a better way.

---

## The Technique

**Switch back to Plan Mode mid-task.**

```
> Hmm, this isn't working. Let's step back and re-plan.
```

Or more explicitly:

```
> Stop. Enter plan mode. We need to rethink this approach.
```

Claude will:
1. Acknowledge the pivot
2. Analyze what went wrong
3. Propose a new approach
4. Wait for your approval before continuing

---

## Example: API Integration Gone Wrong

**Initial task**: "Integrate with the Stripe API for subscriptions"

**30 minutes in, stuck**:
- Realized the existing auth doesn't support webhooks
- The database schema doesn't have subscription fields
- Getting rate limited during testing

**Recovery prompt**:
```
> Stop. This is getting messy. Let's re-plan.
>
> What we've learned:
> - Webhooks need a different auth approach
> - Schema needs subscription fields first
> - We need a test mode for Stripe
>
> Re-plan this integration with these constraints.
```

**Claude responds with new plan**:
```markdown
## Revised Plan

### Phase 1: Foundation (do first)
1. Add subscription fields to schema
2. Create webhook endpoint with separate auth

### Phase 2: Integration (after foundation)
3. Stripe test mode setup
4. Subscription creation flow
5. Webhook handlers

### Phase 3: Production
6. Switch to live keys
7. Add monitoring

Should I start with Phase 1?
```

---

## When to Re-Plan

- **3+ failed attempts** at the same thing
- **Discovered a blocker** you didn't anticipate
- **Scope creep** - the task grew bigger than expected
- **Confusion** - Claude (or you) lost track of what you're doing
- **New information** - you learned something that changes the approach

---

## Pro Tip: Plan Review

The Claude Code team has someone use a **second Claude session** to review plans:

```
> Review this plan as a staff engineer. What's missing? What could go wrong?
```

This catches blind spots before you commit to an approach.

---

## Anti-Patterns

- **Don't re-plan every 5 minutes** - Give approaches time to work
- **Don't re-plan without stating what you learned** - The new plan needs context
- **Don't skip the approval step** - Review the new plan before Claude executes
