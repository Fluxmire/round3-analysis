# Round 3 · phuy26 and TiltGilt/all-in-one: source analysis (read-only, nothing executed)

Both repos fork `SmoothTight/newvictor@0f4bc93`, the round-3 boss ("NV"). Paths are relative to each repo's `miner404/`.
Figures are estimates from source; neither repo contains results.

| | phuy26 | TiltGilt all-in-one | NV boss |
|---|---|---|---|
| Commit | `4f59e66` (11-06 08:14) | `1db2675` (11-05 09:56) | `0f4bc93` |
| Calls per image (nominal) | ~45 | ~37 | ~29 |
| Model-seconds per image | ~6,000 | ~4,850 | ~4,800 |
| Renders per image | ~50 | ~45 | ~40 |
| 128 prompts at 6 / 12 concurrent calls | 36 h / 18 h | 29 h / 14 h | 28 h / 14 h |

## 1. phuy26: phuy29 without two later fixes
Running `diff -r` against phuy29 finds only `pipeline.py` changed. phuy26 lacks two of phuy29's later changes:

1. **Priority tier.** Every call sends `"service_tier": "default"` (`:607`): the same credits, about 20% longer wall time.
2. **Render-500 fix.** After 2 retries, a renderer HTTP 500 becomes a model-visible `RenderRejected` "RENDER_TIMEOUT …
   make it lighter" (`:445-452`), as in NV. That triggers a repair call that a replay would not reproduce, so the entry
   can fail.

**Same as phuy29** (details in `round_2/analysis/phuy29.md`):
- **Plan** (`versions.py:8-17`): 6 Sol-high drafts, keep 3; 4 full Sol-high rewrites + 1 Sol-high patch-edit, all
  `per_lineage=True`; no Astra step.
- **Per-lineage gate:** one mirrored 2-call panel per step; a child must beat its own parent by `PARENT_MARGIN=0.25`
  (`:55`, `:1075-1089`).
- **Final pick:** the panel keeps the top 2, then an 8-call Sol-low copy of the judge's stage 1 decides (`:984`,
  `:1091-1121`), only if the budget leaves the pose panel. Ties go to the panel's first.
- **Neutral light:** views and grid use `lighting="neutral"` (`:84-92`).
- **Fixes and prompts:** the bake re-parent fix and sedge-style material rules.

**Cost:**
- **Calls:** 6 + 2 + 5 × (3 + 2) + 2 + 8 + 2 ≈ 45.
- **Credits:** 3–4M (their smoke run measured 2.6–3.4M).
- **Model time:** ≈ 6,000 s per image.
- **Renders:** ≈ 50 per image, ≈ 6,400 per round (under the 10,000 cap).
- **24 h:** 36 h at 6 concurrent (does not fit), 18 h at 12.

**Verdict:** the same strength as phuy29 but operationally worse. Expect phuy29 ≥ phuy26.

## 2. TiltGilt all-in-one (3 TiltGilt hotkeys, similar code)

### Size of the changes vs NV
- `pipeline.py`: about +300 lines.
- `prompts.py`: 425 → 746 lines.
- `images.py`: about +50 lines.
- `match.py`: about +45 lines.

Comment words were swapped automatically ("fixed"→"style_e", "neutral"→"style_a", …; e.g. `pipeline.py:3`, `:62`,
`:82`), apparently to defeat copy detection; no model-facing text is affected.

Many options are present but off: `verify_fit`, `merge`, `tilt`, `palette`, `colour`, `styles`, `style_d`,
`style_e` (`pipeline.py:512-546`).

### Active plan (`versions.py:7`)
| Stage | What runs | Gate |
|---|---|---|
| Drafts | 6 Sol-high, keep 3 | panel |
| Step 1 | Sol-high rewrite per survivor | panel over 3 children + 3 parents, keep 2, margin 0.25 |
| Step 2 | Astra-high rewrite per survivor, `keep=0` | **none**: the child replaces its parent (`:1184-1188`) |
| Step 3 | `pick='judge'`, `latest=True` | 8-call judge stage-1 copy between the two Astra children (`:1163`) |
| Steps 4–6 | best-of-2 Sol-high children | panel vs parent, margin 0.25 |
| Post | pose panel, then the outline fit with a guard | |

Flag `style_c=True` (`:1024-1027`) does three things:
- renders the grid in neutral light (`:91`, `:770`);
- adds a 1024-px uncropped front sheet to rewrites (`:708`);
- swaps all prompts for the style-C versions (`:611-614`).

### New mechanisms
1. **`judge_pairs`** (`:917-951`): every pair × 4 views × both orders, using the validators' exact stage-1 prompt
   (`prompts.py:279-291`), Sol low with a strict JSON schema. A view counts only when both orders replied. It runs
   mid-chain.
2. **Ungated Astra step + `latest`** (`:1162-1164`, `:1184-1188`): the pre-Astra parents are never compared again.
3. **Bake nested-part fix** (`:328-336`): a part's position is relative to the nearest baked ancestor.
4. **Outline-fit guard** (`:1080-1083`, `images.py:75-107`): refuses the fit if frame fill drops below 95%, or if more
   than 3% more of the object becomes loose.

### Prompt rewrite (style C, `prompts.py:401-525`)
- **Neutral light:** "what you see is what the judge sees" (`:405-415`). NV's "dim studio light … metallic parts …
  turn black" is removed.
- **Darks reversed** (`:423-428`): #111–#222 is now allowed (NV said at least about #2a2a2a); "do not lighten a part to
  be safe".
- **Metal** (`:429-431`): 1.0 renders darker than 0.6 in the judge's light.
- **Build for every camera** (`:418-421`): below, above and the back.
- **Glass** (`:500-506`): MeshPhysical transmission 0.2–0.65, opacity 0.3–0.7, ior 1.5, clearcoat, tint.
- **No stray parts** (`:504-506`): no floating pieces, halos, fuzz shells or patches.
- **Lean rules** (`:119-121`): the `fitToUnitCube` snippet and the 40-line low-poly car example are dropped ("may
  anchor drafts small").
- **Rewrite prompt:** "go through the four front views one at a time"; "replace any simpler stand-in"; "do not lighten
  a part the target shows dark"; "remove anything the target does not show".
- **Selection prompt:** the judge sees views smaller, so shape, proportions, pose and colour outweigh tiny details;
  floating pieces and halos are flaws.

### Cost
- **Calls:** ≈ 37.
- **Credits:** ≈ 2.6–3.0M.
- **Model time:** ≈ 4,850 s per image.
- **Renders:** ≈ 45 per image (≈ 5,800 per round).
- **24 h:** 29 h at 6 concurrent (does not fit), 14 h at 12.

### Risks
- An Astra regression on both lineages cannot be reverted.
- Both duel candidates unparsable → both score 99 and list order decides.
- The render-500 replay risk inherited from NV.
- The docstring says fill 95% beats 99%, but the plan uses 0.99.
- The lean rules may raise first-draft failures.

## 3. What to borrow
1. Neutral-light prompts (TiltGilt) — **our v5/v7 prompts already carry no dim-light advice** (checked: the system
   prompt says only "metalness only for metal"). Still worth borrowing: darks allowed, the metal note, every camera
   counts.
2. Glass and stray-part rules; "below and above count equally".
3. phuy's gate shape: child vs own parent with a margin, then the top 2 go to the duel.
4. `judge_pairs` for N candidates, counting a view only when both orders replied.
5. Best-of-2 Sol-high refine children, gated against the parent.
6. The fit guard, the bake nested-part fix, render 500 as an infrastructure error (with care), the priority tier.

## 4. Judgement (from source, not measured)
- **phuy26:** likely stronger than NV (depth, judge-copy final, neutral light). It needs ≥ 12 concurrent calls; phuy29
  is better.
- **TiltGilt:** likely modestly stronger than NV at the same compute (neutral-matched prompts, judge sheet, best-of-2,
  fixes). Main risk: the ungated Astra step.
- **vs v5 (≈ 10 calls):** both use 2.5–3× more compute and are likely ahead.
- **vs v7 (≈ 18 calls):** phuy26 is likely ≥ v7; TiltGilt is roughly comparable. A gap under about ±10% needs a duel
  on ≥ 32 stems to show.
