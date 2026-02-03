# Debug Mode

**Tip #5**: Claude fixes its own bugs - Give access to logs and let it debug.

---

## The Problem

Something's broken. You could:
1. Read the logs yourself and explain to Claude
2. Copy-paste error messages back and forth
3. Debug manually

Or you could let Claude do it.

---

## The Technique

Give Claude access to the logs and bug context, then step back:

```
> Here's the error log. The bug is [description]. Fix it.
```

Don't micromanage the approach. Claude is "surprisingly capable at pattern-matching across log noise."

---

## Setup: Give Claude Access

### Option 1: Direct Log Access

If logs are in files:
```
> The server logs are in ./logs/server.log.
> Users are reporting [issue]. Find and fix it.
```

### Option 2: Command Access

Give Claude the commands to fetch logs:
```
> You can check logs with: docker logs api-server --tail 100
> The error is happening on checkout. Debug it.
```

### Option 3: Error Thread

Paste the error or link to the issue:
```
> GitHub issue #234 has the full stack trace and reproduction steps.
> Fix it.
```

---

## Example: Distributed System Bug

**The problem**: Payments sometimes fail silently

**The prompt**:
```
> Payments are failing for ~5% of users. No error shown to user.
>
> Relevant logs:
> - Payment service: docker logs payment-svc --tail 500
> - API gateway: ./logs/gateway.log
> - Database: Check slow query log at ./logs/postgres-slow.log
>
> Find the bug and fix it.
```

**Claude's process**:
1. Checks payment service logs for failures
2. Correlates timestamps with gateway logs
3. Finds timeout pattern in slow query log
4. Identifies: database lock contention on high-traffic products
5. Proposes fix: add row-level locking hint

---

## When This Works Best

- **Logs are accessible** - Claude can read them directly
- **Bug is reproducible** - There's a pattern to find
- **Multiple log sources** - Claude excels at correlation
- **You're stuck** - You've already tried the obvious things

---

## Pro Tips

### 1. Start Broad, Then Narrow

```
> Something's wrong with user auth. Start by checking the auth service logs,
> then narrow down from there.
```

### 2. Give Context on Normal Behavior

```
> The payment flow normally takes <2s. Anything over 5s is a bug.
```

### 3. Let Claude Run Commands

If you're in Claude Code, Claude can run the log commands itself:

```
> Debug the failing webhook. You can run any commands needed.
> Start with the webhook logs.
```

---

## Anti-Patterns

- **Don't hide information** - Give access to all relevant logs
- **Don't jump to conclusions** - Let Claude investigate
- **Don't micromanage** - "Check line 47" defeats the purpose
- **Don't ignore Claude's questions** - If it asks for more context, provide it
