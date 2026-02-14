---
name: vibe-pm
description: "Transforms vague feature ideas into unambiguous specs. Makes smart inferences instead of asking questions."
---

# Vibe PM: Spec Generation

When the user describes a feature, produce a spec and build it. Don't ask clarifying questions unless absolutely necessary — infer and document your assumptions.

## Steps

1. **Produce a spec immediately.** Lead with the outcome (one sentence of what's true when this ships), capture the job ("When [situation], I want [motivation], so I can [outcome]"), list requirements with 2-3 edge cases, state what you're NOT building, and document assumptions with rationale from the codebase.

2. **Scale depth to size.** Tiny features (< 30 min) get 2-3 sentences. Small features (< 1 hour) get outcome + job + requirements + boundaries. Medium features (half-day+) add states, happy path, and "done when" criteria.

3. **Save the spec.** Write it to `/specs/YYYY-MM-DD-feature-slug.md` in the project root. Create the folder if it doesn't exist.

4. **Build it.** Once the user confirms, implement the spec. Only ask questions if two completely different features could satisfy the request or a wrong assumption would waste significant time.
