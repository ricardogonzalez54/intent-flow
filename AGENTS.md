# AGENTS.md — Project Entry Point

This project may use **intent-flow**, a lightweight markdown convention for human-AI pair working. Check the bootstrap before applying anything below.

## Bootstrap

1. **Does `intent-flow/` exist at the repo root?**
   - No → this project does not use intent-flow. Ignore the rest of this file.
   - Yes → continue.

2. **Does `intent-flow/` contain `adr/` and `work/`?**
   - Both present → framework is active. `product.md` and `architecture.md` are optional project-level docs; their absence is not a structural error. See "Project-level docs" below.
   - Either missing → tell the human exactly what is missing and ask how to proceed. Do not silently fix.

Do not preload work folders, status files, or project-level docs on session start. Wait for the human to state a task.

## When the human states a task

**A. The task refers to an existing `work/<name>/`.**

Read `status.md` first. Its `phase:` field tells you what to do:

- `defining` → load `intent.md` (draft). Iterate with the human to seal it. Decisions taken during this iteration belong in the `Decisions` section of `intent.md` and will be sealed together with the rest. Do not start implementing.
- `building` → load `intent.md` (sealed) as the anchor. Execute following `status.md → Handoff`. Re-read intent only if drift is suspected.
- `blocked` → surface the blocker. Do not proceed without addressing it.
- `shipped` / `archived` → do not modify unless the human explicitly asks.

**B. The task does not match any work unit.**

Evaluate activation criteria silently. Start a work folder only if at least one applies:

- The task will likely span more than one session.
- The intent is ambiguous and needs conversation before coding.
- Decisions will be made that need to be explainable later.
- Real risk of drift between what is wanted and what gets built.

If none apply, just code. A good commit message is the audit trail.

To start a work folder: copy `intent-flow/work/_template/` to `intent-flow/work/<kebab-name>/`. Write `intent.md` in draft. `status.md → phase: defining`.

## Project-level docs (`product.md`, `architecture.md`)

Optional. When present, they are living docs at project scope: the "what/why" of the project as a whole (product) and "how it currently is" (architecture).

- **Read**: when drafting a new `intent.md` (phase `defining`), if they exist — the new work unit must align with project direction. On demand during `building` if a project-scope question surfaces. Never preload.
- **Update**: when a work unit reaches `phase: shipped`, scan the sealed `intent.md` (already in context) for signals that the work changed product scope or architecture:
  - *Product signals*: new/removed capability, scope change, user-facing behavior change, core principle change.
  - *Architecture signals*: new/removed component, tech stack change, data flow change, new critical dependency, dominant pattern shift.

  If any signal is present, ask the human before loading: *"This work unit likely affected [product / architecture]. Should I review and propose a diff?"* Only load on approval. Propose a diff for the human to apply. Never edit silently. If a relevant file does not exist and signals indicate it should, offer to create it from `intent-flow/work/_template/` siblings — or write it fresh.

  If no signals are present, skip silently. No loading, no question.

## Rules

- A sealed `intent.md` (frontmatter `status: sealed`) is never edited. To revise: create `intent-v2.md` and mark v1 `status: superseded`.
- Cross-cutting decisions go in `intent-flow/adr/`, not in `intent.md`.
- There is no `plan.md` artifact. Plans live in the agent's native task tooling; the next concrete step lives in `status.md → Handoff`.
- Before ending a session inside a work unit, update `status.md → Handoff`.
- Never modify `product.md` or `architecture.md` silently. Always propose a diff and wait for human approval.

For the full operational manual, see `intent-flow/README.md`. Read it only if the conversation calls for it — do not preload.
