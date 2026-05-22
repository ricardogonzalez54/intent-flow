---
status: living
created: 2026-05-17
updated: 2026-05-22
---

# Architecture

`intent-flow` has no runtime. It is a markdown convention plus (eventually) an optional scaffolder. This document describes how the pieces fit together for both adopters and contributors to the framework itself.

For the rationale behind specific architectural choices, see `intent-flow/adr/`.

## Overview

The framework is composed of three concerns, each a separate directory in this repo:

1. **The scaffold** (`scaffold/`) — the deliverable. A literal directory tree that adopter repos receive when they adopt the framework. Pure templates and universal documentation, no instances.
2. **The dogfood** (`intent-flow/`) — the framework applied to itself. Real `product.md`, `architecture.md`, ADRs, and (eventually) `work/` units that govern this very project. Serves both as documentation of the framework's own design and as the first proof-of-use.
3. **The project meta-docs** — repo-root `README.md`, `LICENSE`, this repository's own `AGENTS.md`. These describe the project to GitHub visitors and to coding agents working inside this repo.

The split is what keeps the deliverable clean: adopters never inherit decisions made about intent-flow itself, and contributors never have to second-guess whether a file is an instance or a template.

## Components

### `scaffold/`
- **Purpose:** canonical source for what the v0.2 CLI (`intent-flow init`) will copy into adopter repos. Until the CLI ships, adopters copy this folder manually.
- **Contents:**
  - `AGENTS.md` — agent entrypoint, copied to adopter repo root.
  - `intent-flow/README.md` — operational manual (universal, not adopter-specific).
  - `intent-flow/product.md`, `intent-flow/architecture.md` — empty templates the adopter fills in.
  - `intent-flow/adr/_template.md` — ADR template only. The scaffold contains no real ADRs.
  - `intent-flow/work/_template/` — the two work-unit artifact templates (`intent.md`, `status.md`).
  - `.agent/skills/_template/SKILL.md` — Anthropic Skills convention placeholder.
- **Contract:** everything inside `scaffold/` is template-quality. No content references intent-flow itself, no real decisions, no instances. If it would be wrong in an adopter's repo, it does not belong here.

### `intent-flow/` (dogfood)
- **Purpose:** intent-flow applied to its own development. Validates the framework by being its first user.
- **Contents:**
  - `product.md` — real product document for intent-flow.
  - `architecture.md` — this file.
  - `adr/` — real ADRs documenting cross-cutting decisions about the framework (naming, structural splits, etc.).
  - `work/` — real work units when dogfooding is active. Empty during early v0.1.
- **Contract:** mirrors what an adopter's `intent-flow/` would look like in real use. If something here would not fit in an adopter's structure, that is a signal the framework needs to grow a new convention, not that the dogfood should bend.

### Repo meta-docs
- `README.md` (repo root) — pitch and overview for GitHub visitors.
- `AGENTS.md` (repo root) — agent entrypoint for contributors working on this repo. Functionally identical to `scaffold/AGENTS.md`; they stay in sync.
- `LICENSE` — MIT.

## Data flow

Adopter workflow (current, manual; CLI in v0.2):

```
adopter repo
    │
    │ 1. copy scaffold/* → adopter root
    ▼
adopter has: AGENTS.md, intent-flow/, .agent/
    │
    │ 2. agent reads AGENTS.md on session start
    ▼
agent runs bootstrap check; if intent-flow/ exists with core structure, framework is active
    │
    │ 3. human states a task
    ▼
agent routes per AGENTS.md:
    - existing work unit → read status.md, branch on `phase:` field
        - defining → load intent.md (draft), iterate to seal
        - building → load intent.md (sealed), execute per Handoff
        - blocked / shipped / archived → surface or do nothing
    - new work unit → evaluate activation criteria, possibly create work/<name>/ with intent.md + status.md
```

Contributor workflow on this repo:

```
this repo
    │
    │ same path: AGENTS.md → intent-flow/ bootstrap check
    ▼
intent-flow/ exists (dogfood) → framework engaged on itself
    │
    │ work units for framework evolution go in intent-flow/work/<name>/
    ▼
ADRs about the framework go in intent-flow/adr/, never in scaffold/
```

## External dependencies

None at runtime. The framework requires only:

- A coding agent capable of reading markdown (Claude Code, Cursor, Codex, Aider, etc.).
- Git for history-as-audit-trail (recommended, not strictly required).

v0.2 will introduce an optional CLI; its dependencies will be documented in that version's ADR.

## Deployment

**v0.1 (current):** adopters manually copy the contents of `scaffold/` into the root of their repository. No version pinning, no update mechanism beyond `git pull` on this repo and re-copy.

**v0.2 (planned):** `intent-flow init` CLI copies `scaffold/` contents into the target repo, with idempotency (does not clobber existing files), and an `--update` mode that surfaces drift between adopter files and current templates.

## Known limitations

- **No automated drift detection.** v0.1 relies on manual review of sealed intent vs. delivered code. Tooling is out of scope for v0.1 and likely v0.2 as well.
- **No validation.** Frontmatter shape, phase legality, and supersedence chains are not machine-checked. The CLI in v0.2 may add lint commands.
- **No real-world usage data yet.** The shape was redesigned in v0.1.3 based on first-attempt feedback (too heavy, status under-specified), but the new shape has not yet been exercised on a real project. The first dogfood work units (starting with v0.2 CLI design) are the real test.
- **Single-language documentation.** All templates and docs are English-only.
