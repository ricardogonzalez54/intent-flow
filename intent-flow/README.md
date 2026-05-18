# intent-flow — Operational Manual

This document explains how to work within an `intent-flow/` folder once it has been adopted by a project. For the framework's pitch, philosophy, and rationale, see the [repo-root README](../README.md).

## File map

```
intent-flow/
├── README.md           # this file
├── product.md          # global product intent (living)
├── architecture.md     # current architecture (living)
├── adr/                # cross-cutting decisions, immortalized
│   └── _template.md
└── work/
    ├── _template/      # copy from here when starting a new work unit
    │   ├── intent.md
    │   ├── decisions.md
    │   ├── plan.md
    │   └── status.md
    └── <work-name>/    # one folder per active or historical work unit
```

## When to start a work folder

Activate `intent-flow/work/<name>/` when **at least one** of the following is true:

- The task will likely span more than one session.
- The intent is ambiguous and needs conversation before coding.
- You are making decisions that, in 3 months, you will want to be able to explain.
- There is real risk of drift between what you want and what will get built.

If none apply, just code. Use a good commit message. Git history is a valid audit trail for small changes.

The criterion is **complexity, continuity, and drift-risk** — not the *type* of change. A bug fix that turns into a structural refactor merits a work folder. A 5-minute refactor with no consequences does not.

## The four artifacts

Each work unit has up to four artifacts. Use only the ones the task needs.

### `intent.md` — what we want

The first and most important artifact. Captures the *why* and the *what* in human language, before any implementation thinking.

**Lifecycle:**

1. **Draft.** Created from `_template/intent.md` with `status: draft`. Iterated with the human-AI pair until it captures what is really wanted.
2. **Sealed.** When the human is satisfied, frontmatter changes to `status: sealed` and `sealed: YYYY-MM-DD` is filled in. **From this moment, the file is not edited again.** Git history is the audit trail.
3. **Superseded (rare).** If a major pivot makes the intent obsolete, create `intent-v2.md` referencing the original with a clear "supersedes intent.md because X" header. The original stays as historical record.

The immutability is the point. It is what enables honest review of drift later: "we wanted X, we built Y, was that drift intentional?"

The `type:` field in frontmatter accepts one or more categories (`feature`, `refactor`, `migration`, `infra`, `bugfix`, etc.) so a single work unit can be tagged as multiple types when it genuinely is (common — refactors often resolve bugs, features often require migrations).

### `decisions.md` — work-internal decisions

Living document. Each decision captures: context, what was decided, alternatives considered, rationale. Add entries as decisions are made.

For decisions that affect *multiple* work units or the project as a whole, use `intent-flow/adr/` instead.

### `plan.md` — implementation plan

Living document. The concrete bridge between "what we want" and "what we type into the editor". Lists files to create/modify, ordered steps, testing approach, known risks.

Plans are expected to evolve. Update as the work progresses.

### `status.md` — session continuity

The smallest file, and the one most read at the start of a session. Three sections:

- **Focus** — what is currently being worked on. 1-2 paragraphs.
- **Handoff** — notes for the next session: what was done, what is in progress, what is the next concrete step. Include gotchas.
- **Blockers** — what is blocking progress, or "none".

Write a clear Handoff before ending every session.

## Workflows

### Starting a new work unit

1. Copy `intent-flow/work/_template/` to `intent-flow/work/<your-work-name>/`. Use a short kebab-case name.
2. Decide which layers you need:
   - **Just plan?** You know what you want and how to build it. Skip intent and decisions.
   - **Intent + plan?** You need to clarify what you want, then jump to building.
   - **All three?** Complex work with non-obvious decisions.
3. Write the layer(s) you need, iterating with your coding agent.
4. Once the intent is clear, change its frontmatter to `status: sealed`. From this point, do not edit it.
5. Implement, updating `decisions.md`, `plan.md`, and `status.md` as you go.

### Continuing a work unit

1. Read `status.md` to remember where you were.
2. Read `intent.md` (sealed → source of truth for "what we originally wanted").
3. Read `decisions.md` and `plan.md` for current state.
4. Work. Update `status.md` and `plan.md` as needed.
5. Before ending the session, write a clear Handoff in `status.md`.

### Recording cross-cutting decisions

For decisions that affect multiple work units or the project as a whole (e.g., "use FastAPI", "migrate to PostgreSQL", "support offline mode"), create a new ADR in `intent-flow/adr/`:

```
intent-flow/adr/0001-use-fastapi.md
intent-flow/adr/0002-postgres-with-postgis.md
```

ADRs are immortalized once accepted. To change a decision, create a new ADR that supersedes the old one — never edit an accepted ADR.

## A note on hand-coding without the framework

`intent-flow` does not require pair work for every change, and does not promise that the docs stay in sync with hand-coded changes. If you commit code that touches a sealed work unit without updating its plan or status, you create drift — and that is fine. Drift is auditable when intent is sealed. The framework is a support, not an anchor.

If hand-coded drift becomes systematic, that is a signal to either: (a) seal a new ADR explaining the change, or (b) supersede the affected intent with a new version.

## Frontmatter conventions

All artifacts use YAML frontmatter. Common fields:

| Field      | Used in                                  | Values                                                                     |
| ---------- | ---------------------------------------- | -------------------------------------------------------------------------- |
| `status`   | all                                      | varies by artifact: `draft`/`sealed`/`superseded` (intent), `living` (decisions/plan/product/architecture), `active`/`shipped`/`blocked`/`archived` (status), `proposed`/`accepted`/`superseded`/`deprecated` (ADR) |
| `created`  | `intent.md`, `decisions.md`, `plan.md`   | `YYYY-MM-DD`                                                               |
| `updated`  | living docs                              | `YYYY-MM-DD`                                                               |
| `sealed`   | `intent.md`                              | `YYYY-MM-DD` or `null`                                                     |
| `type`     | `intent.md`                              | array of: `feature`, `refactor`, `migration`, `infra`, `bugfix`, custom    |

Frontmatter enables future programmatic parsing (CLI v0.2+) without breaking v0.1 docs.
