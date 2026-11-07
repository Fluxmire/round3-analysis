# zenai_repos/3rd_round/phuy29 · source analysis (read-only, nothing executed)

Analysed 2026-11-07. Repo `zenai_repos/3rd_round/phuy29` @ `6a2264f2b2d6` (single commit, 2026-11-06 19:51 UTC), by
huypg-zenai (author of the round-2 leader `pgh3`). The claim relayed by the user is "wins sedge by 60%". **No results,
logs or duel files are in the repo** (or anywhere in `zenai_repos/`), so the claim is unverified here.

## What it is
- A **fork of `SmoothTight/newvictor@0f4bc93`**, the round-2 top-2 at +31.3% vs the leader.
- **Unchanged vs newvictor:** `main.py`, `requirements.txt`, `budget.py`, `fallback.py`, `match.py`, `orient.py`,
  `pricing.json`, `agents.md` (0 changed lines each).
- **Changed:**

  | File | Lines | Change |
  |---|---|---|
  | `pipeline.py` | +174 | the main changes below |
  | `prompts.py` | +52 | material/craft rules, judge prompt, patch format |
  | `versions.py` | 7 → 28 | new active plan |
  | `images.py` | +13 | `judge_tiles` |
  | `patch.py` | new | from kaito0311/3d-gen-sub, MIT, credited |

- **Contract:** `async generate(image, seed, witness)`, the same as round-2 winners and our loops.

## Active plan: "beat-all L3P5" (`miner404/versions.py`)
1. **Drafts:** 6 drafts, all Sol high (plain / proportions ×3), with ≤1 repair each. The mirrored panel keeps **3**.
2. **3 lineages kept to the end** (`per_lineage=True`): in each step one panel judges all parents and children, and each
   lineage keeps the better of its parent and its own child. A child must beat the parent by `PARENT_MARGIN`.
   newvictor instead narrows 3 → 2 → 1.
3. **5 rewrite steps per lineage, all Sol high:** 4 full rewrites, then a 5th as a **SEARCH/REPLACE patch edit** (about
   1/3 of the output tokens). **No Astra step**; newvictor has one Astra-high step plus Sol-xhigh steps.
4. **Final pick:** the panel keeps the top 2 lineages, then an **8-call copy of 404's stage-1 judge** (exact judge
   prompts, both orders × 4 front views, Sol low, on the judge's own 1024-px gap-free front renders) decides between
   them. Ties go to the panel's first.
5. **Post:** pose selection and outline fit last, as newvictor.

### Other changes
- **Bake fix:** `__bake404` moves every baked part directly under the root, so nested parts are not offset twice;
  hidden groups stay hidden; `matrixAutoUpdate=false` parts are updated.
- **Neutral grid:** the second-stage grid that rewrites and selection see is rendered with `lighting=neutral`, matching
  the judge since 2026-11-05 (upstream `d41cc70`, the same change we saw in SDK main). Prompts no longer describe dim
  light.
- **Prompt rules:** extra material and craft rules (opacity, ≤ #e6e6e6 whites, MeshBasic only for glowing parts,
  roughness bands, DataTexture patterns, closed surfaces, a long food/organic-object rule).
- **Render 500s:** a render HTTP 500 stays an infrastructure failure, not a model-visible "make it lighter" rejection,
  so replay can't diverge (comment cites `advisor/render500_answer.md`).
- **Priority tier:** every module-writing call uses `service_tier="priority"` (README: about 20% less wall time).
  Verified in the contest source: `witness-gateway/pod_gateway/budget.py` rejects only `"flex"` and charges per model,
  so priority costs the same contest credits. **Unknown:** whether priority burns subscription quota faster.

### The keep-1 duel gate does not run in this plan
`gated()` (an 8-call duel on every keep-1 step) only fires for `keep == 1` steps that are **not** per-lineage. All 5
active steps are per-lineage, so in L3P5 the judge-copy duel runs **once** (final pick). Per-step decisions are made by
the mirrored LLM panel alone.

## Cost and the 24 h window (estimated from source; no run)

**Nominal calls per image ≈ 45** (before repairs; cap 60):

| Stage | Calls |
|---|---|
| Drafts | 6 |
| Draft selection | 2 |
| Rewrites (5 steps × 3 lineages) | 15 |
| Per-step panels (5 × 2) | 10 |
| Final panel | 2 |
| Final duel | 8 |
| Pose | 2 |

- **Credits:** their own note says the "L3 live smoke used only 2.6–3.4M of 5M". Our loop data (Sol-high module ≈ 140K,
  Sol-low judge ≈ 15K) gives a similar ≈ 3–4M.
- **Model time per image:** about 21 Sol-high module calls × ~240 s + about 14 panel calls × ~60 s + 8 duel calls
  ≈ **6,000 s**, about 3× our v5 (≈ 2,100 s).
- **Wall time per image:** about 35–40 min, because the 3 lineages run in parallel.
- **128 images × 6,000 s = 768,000 call-seconds:**

  | Concurrent calls | Time for 128 images |
  |---|---|
  | 6 | ≈ 36 h (**does not fit**) |
  | 12 | ≈ 18 h |
  | 16 | ≈ 13 h |

  Priority tier may cut this by about 20%. It needs a Pod sized for at least 12–16 concurrent captures. The current
  miner guide says the Pod default is 32; in round 1 our Pod stalled at about 8 captures plus render bursts.

## How it relates to our findings
- **Different from our failed merges (loops 2–4):** those let an LLM panel choose *across* families. phuy29 keeps
  selection *within* each lineage (parent vs own child, parent favoured) and saves the only cross-lineage decision for
  an exact copy of the judge's stage 1. That design avoids the failure we measured: the panel swapping in worse
  candidates.
- **Same direction as what helped us:**
  - More rewrite depth on the final candidate (our v5 3rd refine helped; phuy29 has 5 rewrites per lineage).
  - Judge-faithful views: they render neutral light and the judge's own 1024-px front tiles; we added the SIDES sheet.
- **Much more compute:** about 45 calls and ≈ 6,000 s per image vs our v5's ≈ 10 calls and ≈ 2,100 s.

## Assessment of "wins sedge by 60%"
- **No evidence in the repo:** no sample size, judge or protocol stated.
- **Context:** the round-2 top 8 winners are within ±7% of each other on the official 4-stage judge. A +60% margin
  over sedge-wren would be far outside that.
- **Noise:** our own measurement says the same pipeline run twice agrees on only 42% of duels, and a 32-stem local S1
  test swings by about ±25% net. A 60% figure from a small local sample (e.g. ≤ 16 stems or a single run) is
  consistent with a real but much smaller edge.
- **Our read:** plausibly the strongest design we've seen (deep, lineage-preserving, judge-copy final pick, 3× our
  compute), but the 60% is unverified.

## How to verify (needs Codex quota)
- Run phuy29 on our frozen 32-stem sample through the local witness shim, then judge vs sedge-wren's submitted JS and
  vs our loop 7 with the same local S1 judge, same renderer `:8003` (`tools/run_loop.py`, `tools/judge_loop.py`,
  `tools/duel_loops.py`).
- **Quota:** about 45 calls × 32 = about 1,440 calls on one account, likely more than one weekly quota.
- **A 4-stem smoke** (about 180 calls) shows whether it runs and roughly how it compares, not a reliable margin.
- **Requirement:** a working account. Only `~/.codex-3` has no recorded limit, and it is not logged in.
