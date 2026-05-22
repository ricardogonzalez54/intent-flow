---
status: accepted
date: 2026-05-17
---

# ADR-0001: Framework and artifact naming

## Context

The framework was drafted initially under the name `pair-flow`, with artifacts named `intention.md` and a container folder `features/`. During the setup phase it became clear that these names misrepresented the load-bearing concepts of the framework, and would create friction or wrong mental models for adopters.

A decision was needed before the first commit, because renaming after adoption would create unnecessary churn.

## Decision

Three coupled renames were adopted:

| Before        | After         |
| ------------- | ------------- |
| `pair-flow`   | `intent-flow` |
| `intention.md`| `intent.md`   |
| `features/`   | `work/`       |

In addition, a `type:` array field was added to `intent.md` frontmatter so a single work unit can be tagged with multiple categories (e.g., `[refactor, bugfix]`) without forcing a single classification.

## Alternatives considered

- **Keep `pair-flow`.** Rejected: "pair" describes the *method* (pair working), not the *value* (sealed intent as drift anchor). The differentiator versus other spec-driven frameworks is intent immutability, not the pairing pattern itself.
- **Keep `intention.md`.** Rejected: "intent" is the more idiomatic term in current AI/tech vocabulary (intent-driven, user intent, intent-based architectures) and aligns the artifact name with the framework name.
- **Keep `features/`.** Rejected: "feature" implies user-facing functionality and forces classification debates ("is this a feature or a refactor?"). A work unit in this framework can legitimately be a feature, a refactor, a migration, an infra change, or a deep bug fix. `work/` is neutral and accurate.
- **Single `type:` string instead of an array.** Rejected: real work often is genuinely multiple types at once (refactor that fixes a bug, feature that requires a migration). Forcing a single value would either lose information or create artificial debates.

## Consequences

- The framework name now matches its load-bearing concept (intent), which is what enables drift audit later.
- `work/` accepts any unit of work without classification friction; `type:` frontmatter preserves taxonomy without enforcing exclusivity.
- The on-disk working directory was renamed from `pair-flow/` to `intent-flow/` during setup. No git consequences (no commits referenced the old name).
- Future docs, READMEs, and any CLI (v0.2+) must use the new names. Any historical reference to `pair-flow` / `intention.md` / `features/` should be treated as a bug.

## References

- Repo-root `README.md`, `AGENTS.md`, and `intent-flow/README.md` reflect the post-rename names.
