# v5 round 1 — vision critique (T-pose phrasing probe)

GAN-style loop: generator = z-image-turbo-q8_0 via `/render`; discriminator =
Claude vision review of every render against the protocol in
`docs-plan_v5_tpose_round1.jsonld`.

| File | T-pose | Full body | Single tail | Style | Verdict |
|---|---|---|---|---|---|
| probes-tpose_v5-tpose_a_refsheet-s01 | strict T, arms horizontal | head-to-toe, feet + ears in frame | yes | semi-real 3D avatar, dimensional eyes | **PASS** |
| probes-tpose_v5-tpose_a_refsheet-s02 | strict T, arms horizontal | full | yes | ok | **PASS** |
| probes-tpose_v5-tpose_b_modelsheet-s01 | FAIL — arms drooped ~35° (A-pose) | full | yes | ok | FAIL |
| probes-tpose_v5-tpose_b_modelsheet-s02 | FAIL — A-pose | full | yes | ok | FAIL |
| probes-tpose_v5-tpose_c_plainpose-s01 | FAIL — A-pose, armband artifacts | full | yes | ok | FAIL |
| probes-tpose_v5-tpose_c_plainpose-s02 | FAIL — A-pose | full | ambiguous two-tail splay | ok | FAIL |

**Finding:** the bare word "T-pose" is not enough at cfg 1 — the model needs the
arms spelled out. The explicit clause "both arms fully outstretched straight out
to the sides at shoulder height, palms facing down, fingers extended" (variant A)
held 2/2 and is adopted as the v5 pose clause for the feature sweep.
