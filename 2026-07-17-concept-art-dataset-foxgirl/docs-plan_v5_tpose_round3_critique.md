# v5 round 3 — vision critique (reference character, Image #9 rebase)

Identity rebased on `reference-ComfyUI_00446.webp`: short dark brown hair,
amber eyes, off-shoulder navy tunic over white camisole, black cropped
trousers, dark socks, warm beige background. 1 variant x 4 seeds.

| File              | T-pose                                  | Full body           | Single tail | Character match            | Verdict  |
| ----------------- | --------------------------------------- | ------------------- | ----------- | -------------------------- | -------- |
| refchar_tpose-s01 | FAIL — arms drooped ~30°                | full                | yes         | good; ears orange not dark | FAIL     |
| refchar_tpose-s02 | borderline — arms ~20° below horizontal | full                | yes         | good                       | FAIL     |
| refchar_tpose-s03 | strict T, arms horizontal               | full, socks visible | yes         | good; ears orange not dark | **PASS** |
| refchar_tpose-s04 | FAIL — arms drooped ~35°                | full                | yes         | good                       | FAIL     |

**Round-3 findings**

- Pose adherence collapsed to 1/4 with this outfit vs 10/12 in rounds 1–2:
  the loose wide-sleeved off-shoulder tunic fights the T-pose signal much
  harder than the fitted crop top did. The pose clause sits at the END of a
  long prompt; round 4 should move it to the FRONT (z-image weights early
  tokens) and/or strengthen it ("arms raised straight out horizontal at
  shoulder height like airplane wings").
- Ear color ignored: "dark brown fox ears" still renders classic orange fox
  ears (matches v4.1 ears_dark probe difficulty). Round 4: try "black fox
  ears" phrasing.
- Tail rendered orange despite "dark brown fox tail" — same pigment stickiness.
- Zero two-tail splays this round (4/4 single tail with amber eyes pinned).
