# intent-flow

A lightweight, markdown-based framework for human-AI pair working on software projects.

**Driven by intent. Modular by task. Auditable by design.**

## What it is

`intent-flow` standardizes a workflow that already happens informally between developers and AI coding agents: iterating through layers of **intent**, **decisions**, and **implementation plans**. It makes that workflow consistent, modular, and auditable — without the overhead of heavier spec-driven frameworks.

The framework is a convention over markdown files. Any coding agent that reads markdown (Claude Code, Cursor, future tools) can follow it. There is no DSL, no required CLI, no model lock-in.

## What it solves

Existing spec-driven frameworks (Spec Kit, Kiro, BMAD, OpenSpec, Tessl) fall into two failure modes:

1. **Overkill for 90% of tasks.** They impose full pipelines on every change, including bug fixes. A simple feature can generate 1300+ lines of spec text across 8 files.
2. **Rigid pipelines.** You do the full ceremony or you bypass the framework entirely. There is no honest middle.

`intent-flow`'s thesis: **the framework should be modular by task, not by project.** Most work resolves with code + targeted prompts + review. The framework activates only when complexity warrants it — when intent needs clarification, when decisions need to be auditable, when implementation needs explicit planning.

## Core principles

1. **Intent is the anchor.** Every non-trivial unit of work starts from a clear intent. Once that intent is sealed, it is immutable — preserved as the source of truth for "what we originally wanted". This is what enables honest review of drift.
2. **Modular entry.** A unit of work can have 1, 2, or 3 layers (intent → decisions → plan). Use only what the task needs. No forced ceremony.
3. **Continuity via status.** Multi-session work is preserved through a small `status.md` per work unit, with focus + handoff + blockers.
4. **Markdown-first, agent-agnostic.** No tool lock-in. Works with anything that reads markdown.
5. **First-class visibility.** The framework lives at the repo root (`intent-flow/`), not hidden in `.agent/` or similar. It is documentation that happens to also be AI-consumable.
6. **Anti-overengineering.** Single-file status, ADRs only for cross-feature decisions, no CLI required in v0.1.

## When to use it

Activate a work folder (`intent-flow/work/<name>/`) when **at least one** of these is true:

- The task will likely span more than one session.
- The intent is ambiguous and needs conversation before coding.
- You are making decisions that, in 3 months, you will want to be able to explain.
- There is real risk of drift between what you want and what will get built.

If none of those apply, just code. A bug fix with a clear commit message is a perfectly valid form of audit trail.

**Important:** the criterion is *complexity, continuity, and drift-risk* — not the type of change. A bug fix that triggers a structural refactor deserves a work folder. A 5-minute refactor with no consequences does not.

## Structure (what adopters get)

```
your-project/
├── AGENTS.md                       # entry point for coding agents (agent-agnostic)
├── intent-flow/
│   ├── README.md                   # operational manual
│   ├── product.md                  # global product intent (living)
│   ├── architecture.md             # current architecture (living)
│   ├── adr/                        # cross-cutting decisions, immortalized
│   └── work/
│       ├── _template/              # copy from here for new work units
│       └── <work-name>/
│           ├── intent.md           # what we want + decisions (sealed together)
│           └── status.md           # phase, focus, handoff (live)
└── .agent/
    └── skills/                     # reusable technical Skills (Anthropic convention)
```

A "work unit" is any discrete piece of work worth tracking: a feature, a refactor, a migration, an infra change, a deep bug fix. It is the framework's atomic unit. Two files per unit, deliberately. Plans live in the agent's native task tooling; the next concrete step across sessions lives in `status.md → Handoff`. Cross-cutting decisions go in `intent-flow/adr/`.

The `type:` frontmatter field in `intent.md` lets you tag a unit with one or more categories (a single work item can be both `refactor` and `bugfix`).

## Quickstart

1. Copy the contents of [`scaffold/`](./scaffold) into your repo root (manual copy for v0.1; `intent-flow init` CLI is planned for v0.2).
2. Point your coding agent at `AGENTS.md` (auto-discovered by most modern agents — Claude Code, Codex, Cursor, Aider).
3. Start working. Activate work folders only when the criteria above are met.

For the full operational manual — workflow, lifecycle of intent, conventions — see [`scaffold/intent-flow/README.md`](./scaffold/intent-flow/README.md).

## Repo layout (for contributors)

This repository hosts the framework itself and is split into two concerns:

- [`scaffold/`](./scaffold) — the deliverable. Pure templates and universal docs. What `intent-flow init` will copy into adopter repos in v0.2. Nothing here references intent-flow's own internals.
- [`intent-flow/`](./intent-flow) — dogfood. The framework applied to this project (real `product.md`, `architecture.md`, ADRs, and eventually `work/` units). Demonstrates and validates the convention.

If you are adopting intent-flow, you only need `scaffold/`. If you are contributing, see the dogfood for how the framework is meant to be used in anger.

## Status

**v0.1** — Markdown templates and convention. No CLI.

**v0.2 (planned)** — `intent-flow init` CLI to scaffold the structure for new and existing projects.

## Contributing

This project is open source under MIT. Contributions, issues, and discussions are welcome.

If you want to propose a change to the convention itself, open an issue first to discuss — the framework's value is in being small and stable. New features will be considered carefully against the anti-overengineering principle.

## License

[MIT](./LICENSE)
