# v5 round 2 — vision critique (feature deltas in T-pose)

Winning pose clause from round 1 applied to 4 atomic feature deltas, 2 seeds each.

| File | T-pose | Full body | Single tail | Feature delta | Verdict |
|---|---|---|---|---|---|
| silver_hair-s01 | strict T | full, feet visible | yes | silver hair, eyes stayed amber-brown | **PASS** |
| silver_hair-s02 | strict T | full | FAIL — two-tail splay behind skirt | eye color drifted grey-blue with silver hair (register coupling) | FAIL |
| short_dark_hair-s01 | strict T | full | yes | dark bob, rest of baseline held | **PASS** |
| short_dark_hair-s02 | strict T | full | yes | ok | **PASS** |
| kimono_outfit-s01 | T (slight forearm droop, acceptable) | full, sandals | yes | kimono; long sleeves partly obscure arm silhouette | **PASS** (note) |
| kimono_outfit-s02 | near-T, slight droop | full, barefoot | FAIL — two-lobe tail splay | ok | FAIL |
| hoodie_outfit-s01 | ~10° droop below horizontal | full, barefoot | yes | hoodie + shorts | **PASS** (borderline droop) |
| hoodie_outfit-s02 | strict T | full | yes | ok | **PASS** |

**Round-2 findings**

- 6/8 pass. Both failures are the known residual two-tail splay (~15% rate,
  matches v4's 24/28); fix remains re-rolling the seed, not prompt surgery.
- Eye color can couple to hair color (silver → grey-blue eyes on s02);
  consider adding "amber eyes" positively to the identity base in round 3.
- The T-pose clause is robust across outfits; loose sleeves (kimono, hoodie)
  soften the arm line slightly but the pose holds.
- Modelling-reference concern: the floor-length kimono hides the leg
  silhouette entirely — flagged for user decision (shorter kimono variant vs
  accepting hidden legs for that feature).

**Next (round 3 proposal, pending user):** add "amber eyes" to identity base,
re-roll the 2 failed seeds, then scale the sweep (remaining features x 20
seeds) with per-image tail/pose QA.
