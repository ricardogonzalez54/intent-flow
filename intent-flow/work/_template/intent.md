---
status: draft           # draft | sealed | superseded
type: [feature]         # one or more of: feature, refactor, migration, infra, bugfix, <custom>
created: YYYY-MM-DD
sealed: null            # set to YYYY-MM-DD when sealed; do not edit file after this
---

# Intent: <Work Unit Name>

## What we want

A clear, human-readable statement of what this work unit is supposed to deliver and why. Written from the perspective of "what would a user/developer/stakeholder want from this?"

This section should be understandable by someone who has never seen the project. Avoid technical jargon unless the work is intrinsically technical.

## Why this matters

The motivation. What problem does this solve? What changes for the user/system if this exists vs. if it doesn't? If we can't articulate the why, the intent is not ready to be sealed.

## Scope

What is in scope for this work unit. Be explicit about boundaries.

## Out of scope

What is explicitly NOT part of this work unit. This is often more important than what is in scope, because it prevents drift.

## Open questions

Questions that need to be resolved during the pair-review phase before sealing. Once sealed, unresolved questions move to `decisions.md` or `plan.md`.

- [ ] Example: should X support multiple Y, or just one?
- [ ] Example: is Z handled client-side or server-side?

## Notes

Any context that doesn't fit above but matters for understanding the intent.

---

**Lifecycle note:** This document is immutable once `status: sealed`. Do not edit after sealing. If the intent changes substantially, create `intent-v2.md` and mark this one `status: superseded`.
