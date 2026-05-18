---
status: active
created: 2026-05-17
purpose: Kickoff brief for the intent-flow framework project. Read this first.
---

# intent-flow — Kickoff Brief

> Naming history: this project was originally drafted as `pair-flow`. The repo working directory still carries that name on disk; the framework itself was renamed to `intent-flow` during setup because "intent" is the load-bearing concept (intent as sealed anchor), not "pair". See "Naming decisions" below.

## What this is

`intent-flow` is a lightweight, markdown-based framework for human-AI pair working on software projects. It standardizes a workflow that already happens informally — humans iterating with AI coding agents through layers of intent, decisions, and implementation plans — and makes it consistent, modular, and auditable.

It is **not** an agent orchestration framework. It is **not** a multi-agent role-playing system. It is **not** a CLI-first tool. It is a convention over markdown files that any coding agent (Claude Code primarily, but anything that reads markdown) can follow.

## The problem it solves

Existing spec-driven development frameworks (Spec Kit, Kiro, BMAD, OpenSpec, Tessl) fall into two failure modes:

1. **Overkill for 90% of tasks.** They impose full pipelines on every change, including bug fixes. A simple feature can generate 1300+ lines of spec text and 8 files in Spec Kit. This is the "framework cost" problem — they assume infinite token budget and developer patience.
2. **Rigid pipelines.** They don't let you enter at the layer that matches the task's complexity. Either you do the full ceremony or you bypass the framework entirely.

`intent-flow`'s thesis: **the framework should be modular by task, not by project**. Most tasks resolve with handcoded work + specific prompts + review. The framework activates only when complexity warrants it — when intent needs clarification, when decisions need to be auditable, when implementation needs explicit planning.

## Core philosophy

- **Markdown-first, agent-agnostic.** No DSL, no required CLI, no model lock-in. Works with Claude Code, Cursor, any future agent that reads markdown.
- **Work-scoped, not layer-scoped.** All artifacts for a unit of work live together in `intent-flow/work/<name>/`. Navigation friction is minimized.
- **Modular entry.** A work unit can have 1, 2, or 3 artifacts (intent, decisions, plan). You enter at the layer the task needs. No forced ceremony.
- **Intent is immutable once sealed.** The intent document captures what was wanted originally and is never edited after sealing. This creates traceability of drift — anyone (human or AI) can compare original intent to current code and ask honestly: "are we still aligned, or did we drift?"
- **Decisions and plan are living documents.** They evolve as work progresses.
- **First-class visibility.** The framework lives in `intent-flow/` at the repo root, not hidden in `.agent/` or similar. It is human-readable documentation that is also AI-consumable, not the other way around.
- **Anti-overengineering.** Single-file `status.md` for session continuity. ADRs only for cross-cutting decisions. No CLI in v0.1 — just markdown templates and a clear convention.

## Folder structure

```
project/
├── AGENTS.md                       # entry point for any coding agent, points to intent-flow/
├── intent-flow/                    # the framework, first-class and visible
│   ├── README.md                   # operational manual for devs working inside intent-flow/
│   ├── product.md                  # global product intent (the why)
│   ├── architecture.md             # current living architecture
│   ├── adr/                        # cross-cutting decisions, immortalized
│   │   └── _template.md
│   └── work/
│       ├── _template/              # empty templates to copy from
│       │   ├── intent.md
│       │   ├── decisions.md
│       │   ├── plan.md
│       │   └── status.md
│       └── <work-name>/
│           ├── intent.md           # sealed after initial iteration
│           ├── decisions.md        # living
│           ├── plan.md             # living
│           └── status.md           # focus + handoff + blockers in one file
├── .agent/                         # ONLY for things purely consumed by AI
│   └── skills/                     # Anthropic Skills convention
│       └── <skill-name>/
│           └── SKILL.md
└── docs/                           # traditional human docs if needed
```

## Naming decisions

| Old (draft)        | New (current)    | Reason                                                                                            |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------- |
| `pair-flow`        | `intent-flow`    | "Intent" is the load-bearing differentiator (sealed anchor for drift audit). "Pair" describes the *method*, not the *value*. |
| `intention.md`     | `intent.md`      | "Intent" is more idiomatic in tech/AI vocabulary (intent-driven, user intent, intent-based).      |
| `features/`        | `work/`          | A work unit can be a feature, refactor, migration, infra change, deep bug fix. "Feature" implies user-facing and forced classification debates. `work/` is neutral. |
| `type:` (none)     | `type: [...]`    | Frontmatter array on `intent.md` lets a unit be tagged with multiple categories simultaneously, since real work often is (e.g., refactor + bugfix). |

## Key design decisions and rationale

| Decision                                                            | Why                                                                                                            |
| ------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `intent-flow/` at repo root, not `.agent/`                          | First-class visibility. This is documentation (human-readable) that is also AI-consumable, not hidden infra.   |
| Group by work unit, not by layer                                    | Cohesion over separation of types. All artifacts for one work unit in one place.                               |
| Modular entry (1, 2, or 3 layers per work unit)                     | Avoids the "framework cost" that kills Spec Kit/Kiro for small tasks.                                          |
| `intent.md` immutable when sealed                                   | Traceability of drift. Enables honest review of "what was wanted vs what was built".                           |
| `decisions.md` and `plan.md` are living                             | They capture evolution, not initial state.                                                                     |
| `adr/` only for cross-cutting decisions                             | Work-internal decisions stay in `work/<name>/decisions.md`. Don't overload ADRs.                               |
| `status.md` as a single file with sections (focus/handoff/blockers) | One dimension until proven otherwise. Anti-overengineering. Migrate to folder only if it hurts.                |
| Activation criteria based on complexity/continuity/drift-risk       | Not on type of change. A bug fix can warrant a work folder; a refactor may not. The criterion is the *cost of forgetting why*, not the label. |
| Skills live in `.agent/skills/` following Anthropic convention      | Don't reinvent Skills. They are the right primitive for reusable technical patterns.                           |
| Frontmatter (status + updated + type) on every artifact             | Enables future programmatic parsing (CLI v0.2+) without breaking v0.1 docs.                                    |
| No CLI required in v0.1                                             | The framework must work with only markdown + a coding agent. Zero adoption friction.                           |
| Split README: root (pitch) + `intent-flow/README.md` (manual)       | Two distinct audiences — GitHub visitor evaluating adoption vs. dev working inside an adopted project. The inner README is what `intent-flow init` will copy in v0.2. |

## Intent lifecycle (the most important detail)

1. **Draft.** When a work unit is conceived, `intent.md` is created from `_template/intent.md`. Frontmatter `status: draft`. It is iterated with the AI: pair-review for product clarity, technical feasibility, scope.
2. **Sealed.** Once the human is satisfied that the intent captures what they really want, frontmatter changes to `status: sealed` and `sealed: YYYY-MM-DD` is filled. **From this moment, the file is not edited again.** Git history is the audit trail.
3. **Superseded (rare).** If a major pivot makes the intent obsolete, create `intent-v2.md` referencing the original with a clear "supersedes intent.md because X" header. The original stays as historical record.

This is what differentiates `intent-flow` from frameworks where specs are "living" — those frameworks lose the original intent and can't audit drift.

## Activation criteria (when to start a work folder)

Activate `intent-flow/work/<name>/` when **at least one** is true:

- The task will likely span more than one session.
- The intent is ambiguous and needs conversation before coding.
- You are making decisions that in 3 months you will want to be able to explain.
- There is real risk of drift between what you want and what will get built.

The criterion is **complexity, continuity, and drift-risk** — not the *type* of change. If none apply, code directly and rely on commit messages for audit trail.

## Status file convention (v0.1, minimal)

A single `status.md` per work unit, with sections that can be empty:

```md
---
status: active # active | shipped | blocked | archived
updated: 2026-05-17
---

## Focus

What I'm currently working on for this work unit (1-2 paragraphs).

## Handoff

Notes for the next session: what's done, what's pending, gotchas.

## Blockers

Anything blocking progress, or "none".
```

If future use reveals that one dimension isn't enough (e.g., blockers become complex, history matters), migrate `status.md` to a `status/` folder. Don't pre-optimize.

## What was explicitly excluded from v0.1

- CLI / `intent-flow init` command. Goes in v0.2.
- Multi-agent orchestration. Out of scope, possibly forever.
- Automatic drift detection. Manual review is the v0.1 contract.
- Slash commands custom to intent-flow. The framework is agent-agnostic; specific agents can add their own integrations later.
- "Playbooks" as a separate concept. Subsumed by Skills (Anthropic convention) in `.agent/skills/`.

## Current state (as of 2026-05-17)

The framework design is complete. Setup-phase changes done in this session:

- Renamed framework: `pair-flow` → `intent-flow`.
- Renamed artifact: `intention.md` → `intent.md`.
- Renamed container: `features/` → `work/`.
- Added `type:` array frontmatter to `intent.md` for multi-category tagging.
- Reformulated activation criteria around complexity/continuity/drift-risk, not change type.
- Split README into root pitch (`/README.md`) and operational manual (`intent-flow/README.md`).

Next concrete tasks:

1. Create `LICENSE` (MIT) at repo root.
2. Create `.agent/skills/` with a skill template (or at least a placeholder so AGENTS.md reference is valid).
3. Optionally rename the on-disk working directory from `pair-flow/` to `intent-flow/` (no git consequences; nothing tracked depends on the parent folder name).
4. First commit.
5. Begin dogfooding: apply `intent-flow` to its own development. First real work unit will be the v0.2 CLI design.

## How to use this brief

Read this file at the start of the session. It contains everything you need to know about the project. For decisions not covered here, ask the human. Do not invent conventions.
