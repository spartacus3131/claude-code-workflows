# Learning Mode

**Tip #10**: Use Claude to explain unfamiliar codebases, generate diagrams, and identify knowledge gaps.

---

## The Problem

You're working in:
- An unfamiliar codebase
- A language/framework you don't know well
- Legacy code with no documentation

You could read the code yourself, or you could have Claude teach you.

---

## The Techniques

### 1. Codebase Explanation

```
> Explain how authentication works in this codebase.
> Start high-level, then go deeper.
```

Claude will:
1. Find the auth-related files
2. Trace the flow from login to session
3. Explain the key concepts
4. Point out gotchas

### 2. ASCII Diagrams

```
> Draw an ASCII diagram of the request flow from
> frontend to database.
```

Output:
```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Browser   │────▶│  API Route  │────▶│  Database   │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │
       │                   ▼
       │            ┌─────────────┐
       │            │    Auth     │
       │            │  Middleware │
       │            └─────────────┘
       │                   │
       ▼                   ▼
┌─────────────────────────────────────┐
│           Session Store             │
└─────────────────────────────────────┘
```

### 3. Concept Explanations

When you hit something you don't understand:

```
> I don't understand what this useCallback is doing.
> Explain it like I know React but not hooks deeply.
```

Claude adjusts explanation to your level.

### 4. Protocol Walkthroughs

```
> Walk me through what happens when a WebSocket
> connection is established in this app, step by step.
```

---

## Example: Learning a New Codebase

**Day 1 prompt**:
```
> I'm new to this codebase. Give me the 5-minute overview:
> - What does this app do?
> - What's the tech stack?
> - What are the main directories and what's in each?
> - Where should I start reading?
```

**Day 2 prompt**:
```
> Now I need to add a feature to the checkout flow.
> Walk me through the existing checkout code:
> - What files are involved?
> - What's the data flow?
> - What state management is used?
> - What patterns should I follow?
```

---

## Pro Tips

### 1. Ask "Why" Not Just "What"

```
> Why does this use a factory pattern instead of direct instantiation?
```

Gets you architectural understanding, not just mechanics.

### 2. Request Comparisons

```
> How does this error handling approach compare to
> the Result pattern? Pros and cons?
```

### 3. Generate Study Guides

```
> I need to deeply understand React Server Components.
> Give me a learning plan:
> - Key concepts to understand
> - Order to learn them
> - Practice exercises
```

### 4. Create Flashcard-Style Q&A

```
> Generate 10 questions I should be able to answer
> about this codebase's data layer. Include answers.
```

---

## HTML Presentations

The Claude Code team generates HTML presentations for complex topics:

```
> Create an HTML presentation explaining our event
> sourcing architecture. 5 slides, with diagrams.
```

Output is a self-contained HTML file you can open in a browser and share with the team.

---

## Spaced Repetition

Build a skill that identifies knowledge gaps:

```
> Based on the code I've been writing, what concepts
> do I seem weakest on? Generate practice problems.
```

---

## Anti-Patterns

- **Don't ask vague questions** - "Explain this codebase" is too broad
- **Don't skip hands-on** - Understanding ≠ reading, write some code
- **Don't memorize, understand** - Ask "why" until it clicks
- **Don't be embarrassed** - Claude doesn't judge your questions
