# easy-diffusion-traces

Session traces from driving [weftspun/easy-diffusion-mcp](https://github.com/weftspun/easy-diffusion-mcp)
with [taskweft](https://github.com/taskweft/taskweft)'s HTN planner
(`mcp__taskweft__validate` / `plan`).

## Layout

Each dated session directory uses the same structure:

- `docs/` — the planning trace: `domain*.jsonld` (reusable `domain:Definition`,
  with real ISO 8601 action durations), `problem*.jsonld` (`domain:Problem`
  state + `todo_list`), `plan*.jsonld` (planner output: resolved plan,
  status, duration-relative `temporal` STN block), and `VETOES.md` where
  human curation happened. Where wall-clock evidence exists, a `civil_time`
  block anchors `PT0S` to absolute timestamps — that block is this repo's own
  annotation, **not** taskweft output (taskweft's schema has no
  absolute-datetime field; `validate` rejects one). Each `civil_time` step
  records its `source` (log/git/GitHub timestamp, or `"approximate"`).
- `dataset/` — accepted generated images (`.webp`), grouped per feature
- `probes/` — prompt/style probe studies (exploration renders that informed
  a plan revision, kept for the record)
- `rejected/` — human-vetoed waves, preserved rather than deleted and
  documented in `docs/VETOES.md`
- `reference/` — user-provided reference images

## Trace sets

| Directory | What happened |
|---|---|
| `2026-07-17-elixir-mcp-server/` | Building the Elixir MCP server itself (9-step session trace with civil-time spans) |
| `2026-07-17-latent-space-foxgirl/` | Seed sweep from the default foxgirl prompt; first human vetoes (two tails, fox-head-as-tail) |
| `2026-07-17-prompt-exploration-foxgirl/` | taskweft-branched prompt-space search; `docs-plan.jsonld` records which branching mechanisms this taskweft build supports (TwMultiGoal backjumping) and which it rejects (pointer/eq guards, rebac+multigoal) |
| `2026-07-17-concept-art-dataset-foxgirl/` | 9-feature x 20-image atomic-feature dataset. v1→v3 veto history in `docs/VETOES.md`; v4 (current) locks a Trellis-friendly 3D-avatar style chosen by the 9-variant probe study in `probes-style_v4-*` (`docs/plan_v4.jsonld`) |

## Status

The v4 sweep (45 batches, 180 images) is paused as of 2026-07-17 at 20/45
batches: baseline, hoodie_outfit, kimono_outfit, knight_armor complete
(80/180 images, `dataset-<feature>-sNN.webp`);
miko_outfit, silver_hair, a_pose, off_shoulder_outfit, short_dark_hair
pending. Resuming = re-running the executor over `docs-plan_v4.jsonld`
(completed batches are skipped by file-existence check).

Tree depth is capped at `session/file`: every grouping (category, wave,
feature) is a hyphenated filename prefix, e.g.
`rejected-v3_flat_anime_trellis-baseline-s01.webp`.
