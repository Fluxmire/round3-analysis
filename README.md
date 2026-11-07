# 404-GEN round 3: remarkable submissions, what we can learn, can we beat them

Written 2026-11-07 by Claude research.
- **Source:** round-3 `submissions.json` at mirror `ce6e89b` (stage `miner_generation`), boss `SmoothTight/newvictor@0f4bc93`.
- **Method:** all analysis is read-only source review of the exact submitted commits; no competitor code was run.
- **What's in this repo:** analysis and notes only, no competitor code.

## Files
| File | What |
|---|---|
| `field_and_estimate.md` | the whole field (67 hotkeys / 59 sources) by code family + our win-rate estimates |
| `analysis_olauge_taohunter.md` | Olauge-Secret cluster (alder-cove, hazel-swift, heron-gale) + taohunter251/lark-ridge |
| `analysis_phuy26_tiltgilt.md` | huypg-zenai/phuy26 (phuy29 family) + TiltGilt/all-in-one |
| `analysis_every523_sompeer.md` | every523Thing/amberlathe + SomPeer/lowtidesignal |
| `phuy29.md` | huypg-zenai phuy29 (newest phuy, read from a local copy) |

## The field in one picture
**Everyone serious has moved onto the boss's own code (newvictor).** Three clusters matter:

| Cluster | Hotkeys | Design |
|---|---|---|
| **Olauge-Secret patch** (alder-cove ×6 identical, 4 variants, + bottleneck-earbud, fire0205, taohunter251 ×3) | ~16 | newvictor + `each_lineage` (2 lineages kept to the end, a gated child-vs-parent step every time) + `draft_each_start` + **neutral-light grid & prompts** + bake/mirror fixes + an unjudged 22° photo tilt + a regex metal boost; 28–40 calls |
| **"style-preset" tree** (TiltGilt ×3, every523Thing ×5, SomPeer ×4; SomPeer renames every identifier) | ~12 | a newer newvictor tree: neutral-light prompt presets, an **8-call judge-copy duel mid-chain** (`judge_pairs`), an **ungated Astra step**, glass/stray-part rules, a fit guard; 27–37 calls |
| **huypg-zenai phuy family** (phuy26 public; phuy19/23/25/27/28/29 private) | 7 | newvictor + 3 lineages × 5 gated Sol-high rewrites (one patch-edit) + an **8-call judge-copy final duel**, no Astra; ~45 calls; needs ≥ 12 concurrent calls for 24 h |

The rest: lumen-kit ×10, mokabetrade orbit-scribe ×5 (+3 private), small new teams ×11, and **ours ×3**
(`Fluxmire/experiment_v7` = merged v5, `experiment_v71` ×2 = merged v7).

## What we can learn (ranked)
1. **Match the judge's neutral light everywhere.** The judge's stage-2 grid has been neutral since 11-04/05 (upstream
   `d41cc70`). The boss still renders a dim grid and tells the model to lighten dark parts. Every top cluster fixed this.
   - **Us:** our prompts never had the dim advice, and every render we show is neutral.
   - **Still to borrow:** their positive rules: black stays black (#111–#222), metal 1.0 renders darker than 0.6,
     build the underside/top/back, real glass (MeshPhysical), no floating parts/halos/stray patches.
2. **Keep lineages separate and gate every step against its own parent** (Olauge `each_lineage`, phuy `per_lineage`).
   - Our v7 keeps the lineages but its refines are **ungated** (kept whenever they render). Adding a child-vs-parent
     check per step is the next natural change.
3. **Judge-copy duels are now standard** (phuy final, style-preset mid-chain).
   - **Detail to copy:** count a view only when both orders replied.
   - **Warning:** Olauge's own note says their 2-lineage final pick "measured 50% on 27 judged pairs", but that was a
     panel, not a judge copy. v7's duel must be measured, not assumed.
4. **Budget reservations from measured output maxima** (lark-ridge `RESERVE_OUTPUT`) instead of output caps. Full-cap
   reservations silently cut the last refine steps in about half of the prompts. v7 already caps Astra at 24k for the
   same reason.
5. **Robustness:** bake fixes (nested parts offset twice, mirrored instances); a fit guard (`views_match`,
   fill/loose check); bounded overload/503 retries.
6. **Don't copy blind:**
   - Olauge's unjudged 22° tilt and regex metal boost (hazel/heron's relative boost compounds on shared materials:
     a bug).
   - The style-preset tree's ungated Astra hand-off.
   - amberlathe's 6 identical drafts and 250k vertex guidance.

## Can we beat them?
These are estimates from design and our local measurements, not official results. Our v5 measured locally vs the boss:
**+9.4% ±8.8%**.

| Opponent | Our v5 (experiment_v7) | Our v7 (experiment_v71) |
|---|---|---|
| boss newvictor | **likely win** (+5 to +10%) | likely win, if it runs cleanly (never run live) |
| style-preset tree (TiltGilt/every523Thing/SomPeer) | parity (±10%) | parity |
| Olauge-Secret patch | **slightly behind** (−10% to 0) | parity to slightly behind |
| phuy family (phuy29) | **behind** (−15% to 0) | behind to parity |
| lumen-kit, orbit-scribe, new teams | likely ahead (+5 to +20%) | likely ahead |

**Verdict:** we can beat the boss and the lower field. Against the three top clusters we are at parity or slightly
behind, mainly on compute and gating:
- they spend 28–45 calls with 6 drafts and gate every step;
- v5 spends about 10 calls and v7 about 18, with ungated refines.

**Nobody is likely to truly dominate.** All top clusters converge on the same recipe (newvictor base, lineages,
neutral light, judge-copy picks), so the top should land within about ±10%. The Olauge cluster's ~16 hotkeys give it
the most chances to top the board by luck.

## How we'd close the gap (next pipeline, "v8")
1. Keep v7's two lineages and judge-copy final, but **gate each refine against its parent** (cheap mirrored panel or a
   4-call judge copy).
2. **Borrow the neutral-light prompt rules** (black/metal/glass/stray parts/every camera).
3. **Reserve from measured maxima** to free budget for 1–2 more gated refines per lineage. That still fits 24 h at
   about 25 calls and ≈ 3,000 model-s per image (128 items ≈ 18 h at 6 concurrent, 9 h at 12).
4. **Measure the duel's agreement with the judge** before paying for it; if it's at chance, spend those calls on depth
   (lark-ridge's lesson).
