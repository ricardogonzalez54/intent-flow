# intent-flow — Operational Manual

This document explains how to work within an `intent-flow/` folder once it has been adopted by a project. For the framework's pitch and rationale, see the repo-root README.

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
    │   ├── intent.md   # what we want + decisions (sealable)
    │   └── status.md   # phase, focus, handoff (live)
    └── <work-name>/    # one folder per active or historical work unit
```

## When to start a work folder

Activate `intent-flow/work/<name>/` when at least one is true:

- The task will likely span more than one session.
- The intent is ambiguous and needs conversation before coding.
- You are making decisions that, in 3 months, you will want to be able to explain.
- There is real risk of drift between what you want and what gets built.

Otherwise, just code. A good commit message is the audit trail.

The criterion is **complexity, continuity, and drift-risk** — not the type of change. A bug fix that triggers a structural refactor merits a work folder. A 5-minute refactor with no consequences does not.

## The two artifacts

### `intent.md` — what we want (and how we'll get there)

Captures the why, the what, and the decisions taken to realize it. Sealed when iteration is done.

**Lifecycle:**

1. **Draft.** Created from `_template/intent.md`, `status: draft`. Iterated with the agent. During iteration, decisions about how to realize the intent populate the `Decisions` section.
2. **Sealed.** When the human is satisfied, frontmatter changes to `status: sealed`, `sealed: YYYY-MM-DD` filled in. **From this moment, the file is not edited again.** Decisions are sealed alongside the intent.
3. **Superseded (rare).** Major pivot → create `intent-v2.md` with a clear "supersedes intent.md because X" header, mark v1 `status: superseded`.

The immutability is the point. It enables honest drift audit: "we wanted X with these decisions, we built Y — was the divergence intentional?"

`type:` frontmatter accepts one or more categories (`feature`, `refactor`, `migration`, `infra`, `bugfix`, custom). A single work unit can be tagged with multiple types when it genuinely is.

### `status.md` — session continuity

The smallest file, and the one the agent reads first when continuing a work unit.

- **`phase:` frontmatter** — the single field that tells the agent what to do without re-reading anything else. Values: `defining`, `building`, `blocked`, `shipped`, `archived`.
- **Focus** — what is currently being worked on. 1-2 sentences.
- **Handoff** — what was just completed, the next concrete step, gotchas. The single most important section for cross-session continuity.
- **Blockers** — what is stuck, or "none".

Write a clear Handoff before ending every session.

## Where decisions live

| Decision scope | Where it goes |
|---|---|
| Internal to one work unit, taken before sealing | `intent.md → Decisions` section |
| Cross-cutting (affects multiple work units or the project as a whole) | `intent-flow/adr/<NNNN>-<slug>.md` |
| Surfaces after sealing and changes the intent | Create `intent-v2.md`, mark v1 superseded |
| Micro / mechanical | Commit message |

There is intentionally no "living decisions" slot. If a decision surfaces after sealing, isn't cross-cutting, and isn't large enough for intent-v2, a commit message is the right home. The absence of a halfway slot forces honesty about decision size.

## On plans

The framework does not include a `plan.md` artifact. Plans live in the agent's native task tooling (TodoWrite, plan mode, etc.) where they belong — they are designed to evolve during execution, and persisting them as markdown invites stale state. The only persistent plan-like content is `status.md → Handoff`, which carries the *next concrete step* across sessions.

If a work unit is complex enough that you genuinely want a persistent technical plan, add a `plan.md` ad-hoc. The framework will not template it for you, and that is intentional.

## Workflows

### Starting a new work unit

1. Copy `_template/` to `work/<kebab-name>/`.
2. Write `intent.md` in draft. Iterate with the agent — capture What, Why, Scope, Out of scope, Open questions. Decisions surface during iteration and populate the `Decisions` section.
3. When the intent is clear and open questions are resolved (into decisions, ADRs, or out-of-scope), change `status: sealed`, fill `sealed:`, and set `status.md → phase: building`.
4. Execute. Plan and progress live in the agent's native task tools; the next concrete step lives in `status.md → Handoff`.
5. Before ending each session, update Handoff.
6. When done, set `phase: shipped`.

### Continuing a work unit

1. Read `status.md`. The `phase:` tells you what to load:
   - `defining` → load `intent.md` (draft), continue sealing iteration.
   - `building` → load `intent.md` (sealed) as anchor, follow Handoff.
2. Work. Update Handoff before stopping.

### Recording cross-cutting decisions

Create `intent-flow/adr/<NNNN>-<slug>.md` from `_template.md`. ADRs are immortalized once accepted; to change a decision, write a new ADR that supersedes the old one — never edit an accepted ADR.

## Hand-coding without the framework

The framework does not require pair work for every change, and does not promise the docs stay in sync with hand-coded changes. Drift is auditable when intent is sealed. The framework is a support, not an anchor.

If hand-coded drift becomes systematic, that signals either (a) seal a new ADR explaining the pattern, or (b) supersede the affected intent.

## Frontmatter conventions

| Field      | Used in                              | Values                                                                     |
| ---------- | ------------------------------------ | -------------------------------------------------------------------------- |
| `status`   | `intent.md`                          | `draft` / `sealed` / `superseded`                                          |
| `status`   | `product.md`, `architecture.md`, ADR | `living` (product/arch); `proposed` / `accepted` / `superseded` / `deprecated` (ADR) |
| `phase`    | `status.md`                          | `defining` / `building` / `blocked` / `shipped` / `archived`               |
| `type`     | `intent.md`                          | array of: `feature`, `refactor`, `migration`, `infra`, `bugfix`, custom    |
| `created`  | `intent.md`                          | `YYYY-MM-DD`                                                               |
| `updated`  | living docs                          | `YYYY-MM-DD`                                                               |
| `sealed`   | `intent.md`                          | `YYYY-MM-DD` or `null`                                                     |
