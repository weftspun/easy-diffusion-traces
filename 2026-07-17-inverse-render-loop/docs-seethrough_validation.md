# See-Through style-register validation — PASS

Input: `../2026-07-17-concept-art-dataset-foxgirl/probes-tpose_v5-refchar_tpose_posefirst-s03.png`
(rf-detr-gated strict T-pose, semi-real 3D-avatar register).
Run via the official HF Space (`24yearsold/see-through-demo`, 1280px, ~2-3 min).
Artifacts: `validation-seethrough_output.psd` (21 layers), `validation-seethrough_composite.png`.

## Layer inventory (21)

tail, back hair, front hair, legwear, topwear, neck, face, mouth, nose,
ears-l, ears-r, eyebrow-l/r, eyewhite-l/r, eyelash-l/r, irides-l/r,
handwear-l/r.

## Findings

- **Style register survives.** Clean semantic decomposition despite training
  on flat anime illustration; no boundary garbage.
- **`tail` is its own layer** and is fully inpainted — including the portion
  occluded by the leg in the source. Tail-count QA becomes connected-
  components on the tail layer's alpha; tail color is a mean-hue statistic.
- **Fox ears got dedicated `ears-l`/`ears-r` layers** (the human-ear slots
  repurposed — no human ears on this character). Ear-color verification
  ("black fox ears" rendered orange) is now a per-layer color histogram.
- **`topwear` fully inpainted** as a complete garment (sleeves, torso,
  hem) behind nothing missing; `legwear` similarly complete.
- **Defect: feet/socks.** The dark socks are missing from the decomposition;
  the composite fades out at the ankles and `legwear` bleeds ankle skin.
  Extremity handling is the weak spot on this register — matters for
  full-body 3D reference. Workaround candidates: higher resolution input,
  or accept and restore feet from the source via the person mask.

## Consequences

1. Phase-3 graph work on see-through.cpp is justified.
2. Interim: wrap the Python NF4 pipeline (docker/WSL2) as an MCP server and
   add a `decompose` stage to the loop (between gate and lift) for
   per-feature verification: ear/tail/hair color histograms, tail
   connected-component count, outfit-delta isolation.
3. Add a feet-restoration step (source pixels under the rf-detr person mask
   below the ankle keypoints) before any layer-based compositing feeds 3D.
