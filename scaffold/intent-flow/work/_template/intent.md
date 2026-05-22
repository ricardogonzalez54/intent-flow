---
status: draft           # draft | sealed | superseded
type: [feature]         # one or more of: feature, refactor, migration, infra, bugfix, <custom>
created: YYYY-MM-DD
sealed: null            # set to YYYY-MM-DD when sealed; do not edit file after this
---

# Intent: <Work Unit Name>

## What we want

A clear, human-readable statement of what this work unit is supposed to deliver and why. Written from the perspective of "what would a user/developer/stakeholder want from this?"

Avoid technical jargon unless the work is intrinsically technical.

## Why this matters

The motivation. What problem does this solve? What changes for the user/system if this exists vs. if it doesn't? If we can't articulate the why, the intent is not ready to be sealed.

## Scope

What is in scope for this work unit. Be explicit about boundaries.

## Out of scope

What is explicitly NOT part of this work unit. Often more important than what is in scope — it is what prevents drift.

## Open questions

Questions to resolve during iteration before sealing. Once sealed, every open question must have either been resolved into a Decision below, escalated to an ADR, or removed as out of scope.

- [ ] Example: should X support multiple Y, or just one?
- [ ] Example: is Z handled client-side or server-side?

## Decisions

The decisions that shape how this intent will be realized. Populated during the sealing iteration. **Sealed with the rest of the intent — not edited after.**

Each decision: what was decided, alternatives considered, rationale.

- **D1: <short title>.** Decided: <decision>. Alternatives: <briefly>. Rationale: <why>.
- **D2: ...**

If a decision affects multiple work units or the project as a whole, put it in `intent-flow/adr/` instead, and reference the ADR here.

## Notes

Any context that doesn't fit above but matters for understanding the intent.

---

**Lifecycle note:** This document is immutable once `status: sealed`. The Decisions section is sealed with the rest. To revise: create `intent-v2.md` and mark this one `status: superseded`. If a decision surfaces after sealing and is too small for intent-v2 and not cross-cutting enough for an ADR, the right home is a commit message — the absence of a "living decisions" slot is intentional.
