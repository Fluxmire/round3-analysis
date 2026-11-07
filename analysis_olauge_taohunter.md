# Round 3 · Olauge-Secret cluster (alder-cove, hazel-swift, heron-gale) and taohunter251 lark-ridge (read-only)

All four repos fork the boss `SmoothTight/newvictor@0f4bc93`.
- **Changed:** `miner404/{pipeline,prompts,versions}.py`.
- **New:** `miner404/tilt.py`, `miner404/measure_own.py`.
- **Unchanged:** `main.py`, `images.py`, `match.py`, `orient.py`, `fallback.py`. lark-ridge also changes `budget.py`.

The same patch is on 13–16 hotkeys: Olauge-Secret ×11, bottleneck-earbud, fire0205 and taohunter251 ×3. Lines below
refer to alder-cove's `miner404/`.

## 1. alder-cove active plan (`versions.py:7`)
| | alder-cove | boss newvictor |
|---|---|---|
| Drafts | 6 Sol-high, plain/proportions alternating | same |
| Keep | **2**, with `draft_each_start` (best of each start) | 3 |
| Steps | per lineage (`each_lineage`): Sol-high, Astra-high (focus: lineage 0 = materials, 1 = shape), Sol-xhigh, Sol-xhigh, Astra-high, Sol-xhigh | narrowing: Sol-high keep 2 → Astra-high keep 1 → {Sol-high, Sol-xhigh} → Sol-xhigh → Sol-xhigh |
| Final | mirrored select of the 2 lineages, pose, outline fit, **photo tilt, metal pass** | pose, outline fit |

**Flags:**
- **`each_lineage`** (`:1094-1103`): one mirrored Sol-medium panel scores all children and parents. Each lineage keeps
  its child unless the parent leads by more than 0.25. Lineages never merge or die.
- **`draft_each_start`** (`:1063-1074`): the kept drafts come from distinct starts, for diversity.

## 2. Selection
- **Scoring** (`scores()`, `:863-883`): 2 Sol-medium calls in mirrored order, on the 4 zoomed judge front views plus
  the judge grid. Score = front mean + 0.25 × grid (+2 for garbage sides).
- **No judge-copy duel.** The child gets a 0.25 margin; with no verdict, the parents are kept (`:891`).
- **The team's own evidence** (lark-ridge `versions.py:1`): the final pick between two lineages "measured 50% on 27
  judged pairs", i.e. chance.

## 3. New mechanisms
1. **Neutral grid** (`:89`): `GRID_VIEW = RenderOptions(lighting="neutral")`, "pixel-identical to the official grid.png
   … since 11-04". The prompts drop all dim-light advice (prompts.py:62, :169, :268; metal note :93). Probably the
   biggest correctness fix.
2. **Photo tilt** (`tilt.py`; `pipeline.py:991-1001`):
   - Renders a level θ0/φ0 view at 0/8/15/22° and compares outline IoU with a photo mask (`measure_own.py`).
   - Applies **only 22°**, and only if it beats 0° by 0.001.
   - `tilted()` sets an unconditional `root.rotation.x`, so it also tilts objects that aren't flat.
   - Not judged by any model; only a render validates it.
3. **Metal pass** (`:72`, `:1003-1016`): a regex on the code (metalness ≥ 0.25) sets every metal material to metalness
   0.8, roughness 0.2. It never looks at the photo; render-validated only.
4. **`views_match`** (`:569-578`): the 4 front views at 96 px must have a mean absolute difference ≤ 12 after the
   outline fit, or the fit is refused.
5. **Bake fixes:**
   - Merge skips meshes that have children (`:106`).
   - Mirrored instanced meshes get their winding reversed (`:294-319`).
   - Nested parts are no longer offset twice (`:328-334`).
   - A precise flat box (`:257`, `:399`).
6. **Retries:**
   - Model overloads: 5/15/30/60 s with the same request id (`:461-462`, `:638-653`).
   - Renderer 503: retried every 2→10 s with **no limit** (`:451-454`).

**Variants:**
- **hazel-swift:** draft starts plain/proportions/materials/parts; only 4 steps; a relative metal pass with **no
  dedup** (bug: compounds `roughness *= 0.8` across meshes sharing a material).
- **heron-gale:** 4 steps; the same relative pass; a stricter `views_match` limit (8).
- **lark-ridge:**
  - `draft_keep=1`; **10 gated steps** on one lineage (3 Astra-high, 6 Sol-xhigh, 1 Sol-high), strictly serial.
  - `budget.py`: SAFETY 6%, and `RESERVE_OUTPUT` reserves measured maximum outputs instead of the caps (Astra 18.5k vs
    22k, Sol-xhigh 36k vs 44k), because the old reservation "refused the planned second Astra step in about half of
    the prompts" (9,066 calls, none truncated).
  - With one lineage, its focus is always "materials".

## 4. Cost per image (nominal; preview cap 78 per prompt)
| | Calls | Credits wanted | Model-s | Wall | Renders |
|---|---|---|---|---|---|
| boss | ≈ 27–29 | ≈ 5M | ≈ 4.5k | ≈ 33 min | ≈ 40 |
| alder-cove | 36 (realistic ≈ 30, see below) | ≈ 6.5M, truncated to ≈ 4.5–4.8M | ≈ 5.7k | ≈ 39 min | ≈ 46–52 |
| hazel / heron | 28 | ≈ 4.8M | ≈ 4.4k | ≈ 29 min | ≈ 40–44 |
| lark-ridge | ≈ 38–40 | ≈ 6.2M, tail cut (≈ 7–8 of 10 steps run) | ≈ 5.6k | ≈ 61 min | ≈ 45 |

alder-cove's full-cap reservations refuse most step-5/6 children, so it realistically runs about 4 steps. Refused
steps still spend 2 selection calls.

## 5. Risks and bugs
- hazel/heron: the compounding metal pass.
- The tilt and metal pass are unjudged. The 22° tilt can apply to tall objects on a +0.001 IoU gain, fitted on a view
  the judge never uses, and it skews the judge's φ = −30 view.
- alder-cove plans steps its budget cannot run.
- The unbounded 503 loop can hang a prompt until the deadline.
- One shared attempt counter for timeouts and overloads (`:641-653`).
- Pro-child gating over many noisy steps can drift.
- The 2-lineage final pick is at chance by their own measurement.

## 6. What we can borrow
- **Borrow:**
  - Neutral grid + prompt text. Our v5/v7 prompts already have no dim-light advice; our SIDES sheet is neutral.
  - The bake fixes (sedge's wrap differs, so these need porting).
  - `views_match` after any post-transform.
  - `RESERVE_OUTPUT`-style measured-max reservations: v7 caps Astra at 24k for the same reason.
  - Bounded overload and 503 retries.
  - `draft_each_start` (diverse starts).
- **A/B test first:** the photo tilt and the metal pass. Don't copy hazel's relative pass.
- **Measure:** v7's judge-copy duel only earns its 8 calls if it agrees with the judge well above 50%.

## 7. Judgement (from source)
- **vs boss:** slightly stronger, by a few percent. Same backbone and spend, plus the neutral grid, fixes, fit guard and
  two diverse gated lineages, minus truncated tails and two unjudged transforms. **lark-ridge** is the best-argued
  variant.
- **vs v5:** roughly equal or better (our margin −5% to +5%). They spend about 1.7× the credits, about 3× the calls, 6
  drafts vs our 2, and gate every step. This team beat v5 locally as sedge-wren (−9% for us).
- **vs v7:** alder-cove/lark-ridge ≥ v7. They gate every step and go up to 2× deeper; v7's refines are ungated, and its
  pick must be proven above chance.
