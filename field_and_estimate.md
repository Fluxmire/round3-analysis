# Round 3 · field snapshot and our win-rate estimate (2026-11-07 ~09:00 UTC)

- **Mirror:** `ce6e89b`. Round 3, stage `miner_generation` (next stage ETA 2026-11-08 07:59 UTC).
- **Boss:** `SmoothTight/newvictor@0f4bc93` (round-2 winner, `rounds/2/winner.json`).
- **Field:** 67 submissions, 59 distinct sources (README + code-tree fingerprints:
  `round_3/readmes/{readmes,families}.json`).

| Hotkeys | Code family (by layout) |
|---|---|
| 25 | newvictor family (`miner404/`): forks of the boss |
| 13 | private / unreadable (incl. huypg-zenai phuy19/23/25/27/28/29, mokabetrade ×3, Uglejo ×2) |
| 10 | lumen-kit family (`sn17gen/`); research-somthing-t/lumen-kit ×8 |
| 5 | orbit-scribe family (`sceneforge/`, mokabetrade) |
| 3 | sedge family (`miner/lumen.py`): **all 3 are ours** |
| 11 | other layouts (SomPeer `forge/` ×4, shakespeare1630 `crown/` ×3, nova-tinker ×2, Cr0wn0207, duongnd18) |

## Our entries (verified byte-identical to local repos)

| Submission | Hotkeys | Local repo | Pipeline | Tested |
|---|---|---|---|---|
| `Fluxmire/experiment_v7@599237b` | 5CypiQTA | `claude_loop7` | merged v5 | live, 2 × 32 stems, local S1 |
| `Fluxmire/experiment_v71@8a7c66c` | 5HMXGg9D, 5DXDsh3H | `claude_v7_merged` | merged v7 | **offline only** |

## Estimate vs the boss (newvictor)

**Evidence (v5 only):** local S1 vs newvictor's round-2 submitted JS on our 32-stem sample, pooled over loops 7+8
(64 duels):

| W / D / L | Net | Margin | SE |
|---|---|---|---|
| 19 / 32 / 13 | +6 | **+9.4%** | about ±8.8% |

**Expected results** (local stage-1 scale; the official judge is 4-stage GLM-4.6V, and stages 2/3 decide many local
draws):

| Entry | Per-duel W / D / L | Expected margin vs boss | P(margin > 0) | P(margin ≥ +7%) |
|---|---|---|---|---|
| experiment_v7 (v5) | ≈ 30 / 50 / 20 % | ≈ +5 to +10% | ≈ 65–75% | ≈ 45–55% |
| experiment_v71 (v7) | not measured | prior ≈ v5 ± design effect | ≈ v5 if it runs cleanly | — |

**Key risk for experiment_v71:** it never ran live. A crash per prompt returns no module, which is an automatic loss.

## Field context
- **phuy29** (huypg-zenai, newvictor fork with lineages + judge-copy duel) is the strongest-looking design we have read.
  It claims +60% vs sedge, unverified, and needs ≥12 concurrent calls for 24 h.
- **Expected spread:** 25 hotkeys are boss forks, so most of the field should land near boss parity (±10%), as in
  round 2 (top 8 within ±7%).

## Estimate vs the other round-3 repos (not the boss), for experiment_v7 = v5

**Method:** each owner's round-3 code family, plus its round-2 official margin vs the round-2 leader pgh3, plus our
direct local S1 head-to-heads (64 duels each, v5 pooled L7+L8):

| Round-2 opponent | Our v5 net (of 64) | Margin |
|---|---|---|
| sedge-wren | −6 | −9% |
| newvictor | +6 | +9% |
| amber-wren | +9 | +14% |

v5 vs pgh3 locally: +31%. Transitive margins (via pgh3) are rough: ±10–15% on top of local-vs-official
uncertainty.

| Group (round-3 hotkeys) | Round-2 strength | Our expected margin |
|---|---|---|
| Olauge-Secret ×11 (now newvictor forks) | sedge-wren +32.8%, shrike-fell +28.1% | **−10% to +5%** (the strongest team; beat us locally as sedge-wren) |
| taohunter251 ×3 (newvictor forks) | amber-wren +29.7% | −5% to +10% |
| other newvictor forks ×11 (every523Thing ×5, TiltGilt ×3, bottleneck-earbud, fire0205, phuy26) | new, or boss-level | −5% to +10% |
| huypg-zenai private ×6 (phuy19, 23, 25, 27, 28, **29**) | pgh3 = round-2 leader; phuy29 design read | **−15% to 0%** vs phuy29 (deeper, 3× compute, judge-copy pick) |
| mokabetrade orbit-scribe ×5 (+3 private) | +22.7% | 0 to +15% |
| research-somthing-t lumen-kit ×8, morphozhub ×2 | +14.1%, adze +21.9% | +5% to +20% |
| new teams (SomPeer ×4, shakespeare1630 ×3, nova-tinker ×2, Cr0wn0207, duongnd18) | no or low round-2 record | +5% to +25% |

## Can anyone dominate?
- **Unlikely in true quality.** The round-2 top 8 were within ±7% of each other, and most round-3 entries are forks of
  the same boss.
- **Likely on paper.** 67 noisy entries (SD ≈ 8–9% per 128 duels) mean the luckiest few will show +10–15% above
  their true level. Olauge-Secret's 11 hotkeys alone give that team many draws.
- **Real dominance candidate: phuy29**, the only design with a clear structural edge (3 independent lineages × 5
  rewrites, a judge-copy final pick, about 45 calls per image). Risks: its 24 h fit needs ≥ 12 concurrent calls, and it
  uses about 60 previews per image against the new 10,000-per-round cap.
