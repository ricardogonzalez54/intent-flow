# AGENTS.md — Project Entry Point

This project may use **intent-flow** for structured human-AI pair working. Before applying anything in this file, run the bootstrap check below.

## Bootstrap check (do this once at session start)

1. **Does `intent-flow/` exist at the repo root?**
   - **No** → this project does not use intent-flow. Ignore the rest of this file.
   - **Yes** → continue to step 2.

2. **Does `intent-flow/` contain the core structure?**
   Required: `product.md`, `architecture.md`, `adr/`, `work/`.
   - **All present** → framework is intact. Proceed.
   - **Anything missing** → tell the human exactly what is missing and ask whether to (a) restore from templates, (b) proceed without the framework, or (c) something else. Do not silently fix.

3. **Inside `work/<name>/` folders, the canonical artifacts are `intent.md`, `decisions.md`, `plan.md`, `status.md`** — but per the modular-entry principle, a work unit may legitimately have only a subset. That is not a structural error.

4. **Extra files or folders** inside `intent-flow/` are fine — they may be project-specific conventions. Do not flag them.

## Where things live (when the framework is active)

- `intent-flow/product.md` — what this product is, who it's for, core principles
- `intent-flow/architecture.md` — current architecture (living)
- `intent-flow/adr/` — cross-cutting architectural decisions, immortalized
- `intent-flow/work/<name>/` — one folder per work unit:
  - `intent.md` — what we want (immutable once sealed)
  - `decisions.md` — work-internal decisions (living)
  - `plan.md` — concrete implementation plan (living)
  - `status.md` — current focus, handoff, blockers
- `.agent/skills/` — reusable technical Skills (Anthropic convention)

For the full operational manual see `intent-flow/README.md`. Read it only if the conversation calls for it — do not preload.

## How to engage the framework

**Wait for the human to state the task.** Do not preload work folders, do not scan `work/*/status.md`, do not summarize the state of the framework on session start. That bloats context for no benefit.

Once the human states a task, route it:

**Case A — the task matches an existing `work/<name>/` whose `status.md` is `status: active`.**

Always read `status.md` first (it carries the handoff). Then determine the phase from the contents of `intent.md`, `decisions.md`, `plan.md` and load only what that phase needs.

The natural flow is: **draft intent → seal intent → materialize decisions → write plan → execute**. Each phase has a single legal focus.

Valid phases:

1. **`intent.md` is `draft`** → *sealing phase*. Load `intent.md` (+ `decisions.md` if it holds WIP notes from iteration). Resolve open questions. Seal the intent. Do **not** write `plan.md` yet — a plan reflecting an unsealed intent is premature.

2. **`intent.md` is `sealed`, `decisions.md` is absent or only frontmatter** → *materialization phase*. Decisions are how a sealed intent becomes concrete. Load `intent.md` + `decisions.md`. Translate the intent into design decisions (architecture choices, trade-offs, constraints). Do **not** write `plan.md` yet.

3. **`intent.md` is `sealed`, `decisions.md` is populated, `plan.md` is absent or skeletal** → *planning phase*. Load `intent.md` + `decisions.md`. Write `plan.md` reflecting both. The plan must trace back to specific decisions.

4. **`intent.md` is `sealed`, `decisions.md` is populated, `plan.md` has concrete steps** → *execution phase*. Load `plan.md` + `status.md` only. Do **not** re-read `intent.md`/`decisions.md` on every turn — that bloats context. Re-load them only if drift is suspected: a plan step doesn't match a decision, a new decision is needed, or the human questions "why".

5. **No `intent.md`, no `decisions.md`, only `plan.md` (modular plan-only skip)** → *execution-only*. Load `plan.md` + `status.md`. Execute. The work unit was deliberately scoped without intent/decisions. Do not propose adding them unless complexity surfaces.

Malformed configurations — do **not** proceed silently; surface to the human and ask how to fix:

6. **`intent.md` is `draft` AND `plan.md` has concrete steps** → premature plan. Ask whether to seal intent first or discard the plan and revisit.

7. **`intent.md` is `sealed`, `decisions.md` is empty, `plan.md` has concrete steps** → materialization was skipped. Decisions should travel with sealed intent. Ask whether to backfill `decisions.md` (recommended) or explicitly document that decisions were trivial.

8. **`decisions.md` exists without `intent.md`** → orphaned decisions. Ask whether `intent.md` is missing or the content belongs somewhere else.

9. **`intent.md` is `superseded` with no `intent-v2.md` present** → broken supersedence. Ask whether v2 was lost, never written, or the work unit should be archived.

**Case B — the task does not match any active work unit.**

Evaluate the activation criteria silently. Start a work folder only if **at least one** is true:

- The task will likely span more than one session
- The intent is ambiguous and needs conversation before coding
- Decisions will be made that need to be explainable later
- Real risk of drift between what you want and what gets built

If none apply, just code. A good commit message is the audit trail.

The criterion is **complexity, continuity, and drift-risk** — not the type of change.

## When a work unit is in focus

- Never edit a sealed `intent.md` (frontmatter `status: sealed`). If the intent needs revision, propose creating `intent-v2.md` and marking the original `superseded`.
- Update `decisions.md` when a decision is made (or `adr/` if it is cross-cutting).
- Update `plan.md` when the plan changes.
- Before ending the session, write a clear Handoff in `status.md`.

## When in doubt

Don't invent conventions. Ask the human.
