---
status: living
created: 2026-05-17
updated: 2026-05-22
---

# Product

## What this is

`intent-flow` is a lightweight, markdown-based convention for human-AI pair working on software projects. It standardizes a workflow that already happens informally — developers iterating with coding agents through layers of intent, decisions, and implementation plans — and makes it consistent, modular, and auditable without imposing a heavy pipeline.

It is a convention over markdown files. Not a CLI, not an agent orchestrator, not a DSL. Any coding agent that reads markdown can follow it.

## Who it's for

Developers who work in pair with AI coding agents (Claude Code, Cursor, Codex, Aider, and successors) and want:

- A way to capture and seal original intent so drift between "what we wanted" and "what we built" can be audited honestly.
- Session continuity across multi-session work units, so context survives when the agent does not.
- An entry-point structure that any modern coding agent can discover and follow without custom tooling.

It is **not** for teams that already have a heavy spec-driven process and need orchestration, nor for tasks small enough that a clear commit message is already the right audit trail.

## Why it exists

Existing spec-driven frameworks (Spec Kit, Kiro, BMAD, OpenSpec, Tessl) fall into two failure modes:

1. **Overkill for 90% of tasks.** They impose full pipelines on every change, generating hundreds or thousands of lines of spec text per feature.
2. **Rigid pipelines.** Adopters either pay the full ceremony or bypass the framework entirely. There is no honest middle.

`intent-flow`'s thesis: **the framework should be modular by task, not by project.** Most work resolves with code + targeted prompts + review. The framework activates only when complexity warrants it.

## Core principles

1. **Intent is the anchor.** Every non-trivial work unit starts from a clear intent. Once sealed, the intent is immutable — preserved as the source of truth for "what we originally wanted". This enables honest drift review.
2. **Modular entry.** A work unit can use 1, 2, or 3 layers (intent → decisions → plan). Use only what the task needs.
3. **Continuity via status.** Multi-session work is preserved through a small `status.md` per work unit.
4. **Markdown-first, agent-agnostic.** No tool lock-in, no DSL, no required CLI.
5. **First-class visibility.** The framework lives at the repo root, not hidden in `.agent/`. It is documentation that happens to also be AI-consumable.
6. **Anti-overengineering.** Single-file status, ADRs only for cross-cutting decisions, no CLI in v0.1.

## Out of scope

- Multi-agent orchestration or role-playing systems.
- Automated drift detection. v0.1 is manual review only.
- A required CLI. Planned for v0.2 as an optional scaffolder, never as a runtime dependency.
- Custom slash commands per agent. The framework stays agent-agnostic; specific agents may add their own integrations.
- Acting as a project management or issue tracker.

## Current state

**v0.1** — Markdown templates and convention. Shipped.

**v0.1.2** — Repo split into `scaffold/` (deliverable for adopters) and `intent-flow/` (dogfood). ADR-0001 documents the original naming decisions. Setup-phase scaffolding removed.

**v0.1.4 (current)** — Project-level docs (`product.md`, `architecture.md`) made optional, with explicit rules: read on `defining`, propose updates only on `shipped` if the sealed intent contains product/architecture signals, never edit silently, two human checkpoints. Closes the last unspecified surface before v0.2.

**v0.1.3** — Framework reduced to its primitive form, two artifacts per work unit:

- `intent.md` (sealable, includes its own `Decisions` section — sealed together).
- `status.md` (live, with `phase:` field as the single bootstrap-read signal).

`decisions.md` and `plan.md` removed as standard artifacts. Plans live in the agent's native task tooling; cross-session continuity lives in `status.md → Handoff`. AGENTS.md compressed from ~95 lines to ~50, with three operative phases (`defining` / `building` / `shipped`, plus `blocked` and `archived`) instead of the previous five.

The promises of the framework — sealed-intent drift audit, session continuity, pair alignment — are intentionally delivered with the minimum surface that delivers them. Anything more would be ceremony.

**v0.2 (planned)** — `intent-flow init` CLI that copies the contents of `scaffold/` into new and existing projects. This is the first real dogfood work unit: the CLI design will be done *using* the v0.1.3 framework on this very repo.
