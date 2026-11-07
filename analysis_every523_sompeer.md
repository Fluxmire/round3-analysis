# Round 3 · every523Thing/amberlathe and SomPeer/lowtidesignal (read-only, nothing executed)

## 0. Origin: one newer newvictor development tree, made to look like separate teams
- **amberlathe** (every523Thing ×5) keeps newvictor's file names.
- **SomPeer `forge/`** (×4) is the same tree with every identifier renamed and comments stripped:
  - `pipeline` → `workflow`, `prompts` → `wording`, `StagePlan` → `Blueprint`, `keep` → `retain`, `style_c` →
    `preset_c`, …
  - Same JS wrapper including the bake fix (`S/workflow.py:303`), same ids and constants (`:27-62`), same duel
    (`rate_pairs`, `:860-893`).
  - Every prompt text is verbatim (124 differing lines, all names or assignments).
- **TiltGilt** (×3, `analysis_phuy26_tiltgilt.md`) carries the same style presets, `judge_pairs` and automatic
  comment-word swaps ("fixed" → "style_e", "neutral" → "style_a"; amberlathe `pipeline.py:3`, `:18`, `:84`).
- **Conclusion:** about 12 hotkeys from one development tree. Nothing comes from sedge-wren, lumen-kit or
  merlin-bluff.

## 1. Pipelines
**amberlathe** (`versions.py:7`):
1. 6 Sol-high drafts, all with **identical** prompts (a reworded "mirror" system prompt, `prompts.py:860-897`;
   plain and proportions map to the same text, `:889-890`). The panel keeps 3.
2. 3 Sol-high rewrites; the panel keeps 2.
3. 2 Astra-high rewrites, **not judged** (`keep=0`).
4. 8-call Sol-low judge-copy duel between the two newest modules (`:925-959`, `:1207-1208`).
5. 2 Sol-high rewrites; the panel keeps 1.
6. Pose, then an outline fit with a fill/loose guard (`:1104-1118`).
- Neutral grid (`:93`, `:778`); every call's text goes through `light_note_e`.

**SomPeer:** the same plan plus one more 2 × Sol-high phase, with the style_c prompts (no style_e metal/gem rules) and
real plain/proportions draft variety.

## 2. Selection
- **Panels:** mirrored Sol-medium, labels shuffled with the round seed, the image hash and the request id; parent
  +0.25.
- **Duel:** the validators' exact stage-1 wording, strict JSON, Sol low; a view counts only if **both orders replied**
  (`:954-958`).

## 3. Prompt changes vs newvictor (neutral light)
- "What you see is what the judge sees"; the back shows at full brightness in two grid views (`:405-415`).
- Black stays black (#111–#222); metal 1.0 renders darker than 0.6 (`:424-432`).
- Glass as MeshPhysical (transmission 0.2–0.65, ior 1.5); a ban on floating pieces, halos and stray patches;
  "remove anything the target does not show".
- **style_e** (amberlathe only):
  - metals 0.6–0.8 with bright/dark bands;
  - never use emissive to brighten a part;
  - high-contrast gem facets.
- **Rewrite header:** an uncropped judge-framed sheet; "below and above as carefully as left and right"; "replace any
  simpler stand-in".
- **Selector:** "the judge sees them smaller … shape, proportions, pose and colour outweigh tiny details".
- **"Mirror" prompts (amberlathe):** the vertex guidance rises from 60k to 250k.
- **Fixes:** the bake nested-mesh fix (`:330-338`) and the fit guard.

## 4. Cost (nominal)
| | amberlathe | SomPeer | boss |
|---|---|---|---|
| Calls | 27–29 | 31–33 | ≈ 29 |
| Model time | ≈ 3.65k s | ≈ 4.25k s | ≈ 4.2k s |
| Renders | ≈ 29–35 | ≈ 33–39 | ≈ 33 |
| 128 prompts at 6 / 12 concurrent | ≈ 22 h / 11 h | ≈ 25.6 h / 12.8 h | ≈ 24 h / 12 h |

- **Credits:** about 3.9–5M against 4.8M usable. Worst-case reservations likely cut the last rewrites, and SomPeer's
  extra phase often.

## 5. Risks
- **Replay looks deterministic** (seeded shuffles, fixed ids, main-thread budget).
- **The Astra hand-off is not judged.**
- **amberlathe:** 6 identical draft prompts; 250k vertex guidance (render timeouts, more repairs); flat objects may be
  tilted twice.
- **Less final refinement than the boss:** no xhigh, 3–4 rewrite steps vs 5.

## 6. Borrow / judgement
- **Borrow:**
  - The neutral wording for black, metal, emissive, gems and glass.
  - The anti-artifact rules.
  - The uncropped judge sheet plus the "below/above" pass.
  - The bake fix and fit guard.
  - Counting a duel view only when both orders replied: apply to v7.
- **vs boss:** parity to about +10%, mainly from matching the neutral grid.
- **vs our v5/v7:** rough parity, within noise. Neither looks stronger than phuy29.
