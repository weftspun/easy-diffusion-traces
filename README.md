# easy-diffusion-traces

Session traces from driving [weftspun/easy-diffusion-mcp](https://github.com/weftspun/easy-diffusion-mcp)
with [taskweft](https://github.com/taskweft/taskweft)'s HTN planner
(`mcp__taskweft__validate` / `plan`). Each dated directory holds:

- `domain.jsonld` — the reusable `domain:Definition` (actions/methods), with
  real ISO 8601 action durations
- `problem.jsonld` — the `domain:Problem` instance (state + `todo_list`)
- `plan.jsonld` — the planner's output (resolved plan, solution tree, status,
  duration-relative `temporal` STN block). Where wall-clock evidence exists,
  a `civil_time` block anchors `PT0S` to absolute timestamps — that block is
  this repo's own annotation, **not** taskweft output (taskweft's schema has
  no absolute-datetime field; `validate` rejects one). Each `civil_time` step
  records its `source` (log/git/GitHub timestamp, or `"approximate"`).
- generated images as `.webp`, grouped per feature, with human-vetoed waves
  preserved under `rejected_*` directories and documented in `VETOES.md`

## Trace sets

| Directory | What happened |
|---|---|
| `2026-07-17-elixir-mcp-server/` | Building the Elixir MCP server itself (9-step session trace with civil-time spans) |
| `2026-07-17-latent-space-foxgirl/` | Seed sweep from the default foxgirl prompt; first human vetoes (two tails, fox-head-as-tail) |
| `2026-07-17-prompt-exploration-foxgirl/` | taskweft-branched prompt-space search; `plan.jsonld` records which branching mechanisms this taskweft build supports (TwMultiGoal backjumping) and which it rejects (pointer/eq guards, rebac+multigoal) |
| `2026-07-17-concept-art-dataset-foxgirl/` | 6-feature x 20-image atomic-feature dataset (v1→v3 veto history), plus the v3.1 reference-image extension (`reference/`, `problem_v3_1.jsonld`, `plan_v3_1.jsonld`). Interrupted mid-sweep 2026-07-17; keepers so far under per-feature dirs |

## Status

The v3/v3.1 sweeps were paused mid-render on 2026-07-17 (user request);
completed so far: `baseline` s01–s04 and the reference image. Remaining
batches resume from the recorded plans.
