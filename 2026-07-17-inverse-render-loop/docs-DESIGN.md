# Closed-loop inverse rendering (v1 design)

Devised 2026-07-17 from five components already in or adjacent to
[github.com/weftspun](https://github.com/weftspun): easy-diffusion-mcp,
taskweft, trellis2cpp, rf-detr-mcp, and one idea from
[Kyvo](https://github.com/AadSah/kyvo). Goal: recover 3D from generated
character art **more correctly** than the current open-loop
prompt → render → TRELLIS flow, while trimming every part that isn't
load-bearing.

## The idea taken from Kyvo — and what was trimmed away

Kyvo aligns language, images, and a _structured scene JSON_ in one token
space, and checks understanding by rendering the scene back. We keep exactly
two things: **the structured scene as the canonical intermediate** and
**render-cycle consistency as the correctness metric**. We drop Kyvo's
Llama-3.2-1B, VQGAN codebooks, and torchtune fine-tuning entirely: it is
trained on CLEVR-style block scenes and would need a full fine-tune to say
anything about foxgirls — and we don't need a learned inverse model, because
**we generate the images ourselves, so ground-truth structure is free**.
Inverse rendering becomes analysis-by-synthesis with measurements.

The structured scene = `prompt_components` from
`../2026-07-17-concept-art-dataset-foxgirl/docs-plan_v5_tpose_round4.jsonld`
(pose, framing, species, ears, tail, hair, eyes, style, outfit, background).
One feature delta = one component swap — separable by construction.

## The loop (see docs-plan.jsonld; capability-routed by taskweft)

| #   | Stage             | Component                            | What happens                                                                                                                                           |
| --- | ----------------- | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | define components | taskweft                             | scene JSON + seeds planned, committed as trace                                                                                                         |
| 2   | render batch      | easy-diffusion-mcp                   | z-image-turbo, T-pose clause fronted, cfg 1                                                                                                            |
| 3   | measure structure | rf-detr-mcp                          | `analyze_pose` (COCO-17 angles) + `segment_image` (person mask)                                                                                        |
| 4   | gate              | curator                              | strict T (arms ≤ 15° from horizontal), head+feet in frame, exactly 1 person, mask solidity; fail → reseed, never prompt-surgery for stochastic defects |
| 5   | lift 3D           | trellis2cpp                          | image → GLB, 512³ fine (1024 cascade only for final accepted assets)                                                                                   |
| 6   | reproject         | trellis2cpp                          | server replay frames / turntable render of the GLB, front view first                                                                                   |
| 7   | cycle score       | curator (using rf-detr measurements) | rf-detr on the reprojection vs the source image: normalized keypoint L2, person-mask IoU, confidence drop                                              |
| 8   | accept / reseed   | curator                              | pass → dataset + GLB committed; fail → veto recorded, reseed from stage 2                                                                              |

Correctness comes from catching each failure at its cheapest stage: pose
drift dies at stage 4 (~20 s/image), bad geometry dies at stage 7 (~2 min),
and nothing reaches human review except calibration and final acceptance.

## Trim list (what v1 deliberately does NOT contain)

- **Kyvo's model stack** (LLM, VQGAN, fine-tuning) — replaced by known
  ground truth + detector measurements.
- **Python TRELLIS runtime** — trellis2cpp is C++/ggml, no PyTorch at runtime.
- **Multi-detector ensemble** — one RF-DETR family covers detection,
  segmentation, and keypoints; rf-detr runs CPU so the 4090 alternates
  between Easy Diffusion and trellis2cpp (`-unload-idle` on both sides).
- **Multi-view diffusion / SfM** — TRELLIS.2 lifts from a single verified
  front T-pose view.
- **Negative prompts** — mathematically inert at cfg 1 (probe-validated).
- **Per-image human review** — humans set thresholds and veto finals only.
- **Semantic-ID / FSQ repos** — downstream consumers, not loop members.

## Validated so far

- Stages 2–4 ran end-to-end this session (v5 rounds 1–4): the rf-detr gate
  matched human verdicts 4/4 with keypoint confidence ≥ 0.75 on this art style.
- trellis2cpp: complete image→GLB pipeline with persisted replay frames
  (stage 6 comes free from its `generations/` store) — not yet built on this
  machine.

## Next steps

1. Build trellis2cpp (CUDA lib + GGUF conversion, ~7 GB weights) and run one
   gated round-4 image through image→GLB.
2. Calibrate stage-7 thresholds: run the loop on the 1 known-good and 3
   known-bad round-3/4 images; thresholds go in the next plan revision.
3. Promote the executor to consume docs-plan.jsonld directly (same pattern
   as execute_plan_v4/v5).
