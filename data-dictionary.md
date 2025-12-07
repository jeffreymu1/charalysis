# Data Dictionary

## Overview

- Unit of observation: one tactical challenge match  
- Target variable: `outcome` (binary win/loss)  
- Team slots:
  - Attacker strikers: `a1, a2, a3, a4`
  - Attacker specials: `as1, as2`
  - Defender strikers: `d1, d2, d3, d4`
  - Defender specials: `ds1, ds2`

Unit ID columns (categorical): `a1,a2,a3,a4,as1,as2,d1,d2,d3,d4,ds1,ds2`  
Character identifiers used to merge per-character attributes into slot-prefixed columns.

---

## Slot prefix

| prefix | team | role | slots |
|---|---|---|---|
| `a1_*...a4_*` | attacker | name of striker (on-field) | `a1,a2,a3,a4` |
| `as1_*...as2_*` | attacker | name of special (support) | `as1,as2` |
| `d1_*...d4_*` | defender | name of striker (on-field) | `d1,d2,d3,d4` |
| `ds1_*...ds2_*` | defender | name of special (support) | `ds1,ds2` |

---

## Core target

| col | type | meaning |
|---|---|---|
| `outcome` | binary (0/1) | match outcome label (win/loss) |

---

## Suffix glossary of per-unit attributes

These appear as `{slot}_{suffix}` (e.g., `a3_atk`, `d1_role`, `ds2_ex_cost`).  
“Applies to” indicates where columns appear in the dataset.

### A) Identity

| suffix | type | meaning |
|---|---|---|
| `atk_type` | categorical | attack type/color used in matchup effectiveness |
| `def_type` | categorical | defense type/color used in matchup effectiveness |
| `role` | categorical | striker role (e.g., dealer/tank) |
| `weapon` | categorical | weapon used |
| `position` | categorical | formation position (front/middle/back) |
| `unit_type` | categorical | special subtype/class label |
| `alt_count` | numerical | number of alternate variants |
| `invul` | categorical | invulnerability capability flag |
| `deals_dmg` | categorical | whether unit can deal direct damage |
| `heals` | categorical | whether unit can heal other units |
| `heals_team_or_self` | categorical | if the unit can heal, the healing scope |
| `generates_cost` | categorical | whether the unit generates EX cost |
| `taunts` | categorical | taunt ability capability flag |

### B) Skill timing and costs

| suffix | type | meaning |
|---|---|---|
| `ex_cost` | numeric | EX skill cost |
| `ex_length` | numeric | EX duration proxy |
| `na_cost` | numeric | normal-attack cost proxy |

### C) Numeric unit stats

| suffix | type | meaning |
|---|---|---|
| `max_hp` | numeric | max HP |
| `atk` | numeric | attack stat |
| `def` | numeric | defense stat |
| `healing` | numeric | healing stat |
| `accuracy` | numeric | accuracy stat |
| `evasion` | numeric | evasion stat |
| `crit` | numeric | critical chance stat |
| `crit_dmg` | numeric | critical damage stat |
| `stability` | numeric | stability stat |
| `min_dmg` | numeric | minimum damage |
| `cc_power` | numeric | crowd-control power |
| `cc_res` | numeric | crowd-control resistance |
| `recov_boost` | numeric | recovery boost |
| `atk_spd` | numeric | attack speed |

### D) Striker-only numeric stats

| suffix | type | meaning |
|---|---|---|
| `dmg_reduction` | numeric | damage reduction |
| `crit_res` | numeric | crit resistance |
| `crit_dmg_res` | numeric | crit-damage resistance |
| `n_range` | numeric | normal attack range |
| `mov_spd` | numeric | movement speed |
| `block_rate_bonus` | numeric | block rate bonus |
| `def_piercing` | numeric | defense piercing |
| `mag_count` | numeric | magazine/ammo count proxy |

### E) Equipment

| suffix | type | meaning |
|---|---|---|
| `equip_slot1` | categorical | equipment category slot 1 |
| `equip_slot2` | categorical | equipment category slot 2 |
| `equip_slot3` | categorical | equipment category slot 3 |

---

## Affinity/terrain

These are slot-prefixed and reflect terrain affinity mappings retained in the final dataset (here: `indoor`).

| feature pattern | type | meaning |
|---|---|---|
| `{slot}_affinity_indoor_dmg_mult` | numeric | damage multiplier under indoor terrain affinity (from `terrain.csv`) |
| `{slot}_affinity_indoor_block_r` | numeric | block-rate modifier under indoor terrain affinity |

---

## Team aggregate features

These summarize team-wide strength for selected numeric stats.

### Aggregate naming conventions

| feature pattern | type | meaning |
|---|---|---|
| `def_{stat}_sum_FULL` | numeric | def sum across defender slots (`d1–d4, ds1–ds2`) |
| `def_{stat}_mean_FULL` | numeric | def mean across defender slots |
| `def_{stat}_std_FULL` | numeric | def std across defender slots |
| `atk_{stat}_sum` | numeric | atk sum across attacker slots (`a1–a4, as1–as2`) |
| `atk_{stat}_mean` | numeric | atk mean across attacker slots |
| `atk_{stat}_std` | numeric | atk std across attacker slots |
| `diff_{stat}_{agg}` | numeric | `atk_{stat}_{agg} - def_{stat}_{agg}` |

**Stats included:**  
`{stat} ∈ {ex_cost, ex_length, max_hp, atk, def, healing, accuracy, evasion, crit}`  
`{agg} ∈ {sum, mean, std}`

---

## Team composition features (categorical proportions over strikers)

Computed over striker slots only (`a1–a4` and `d1–d4`). _FULL denotes availability of whole team info.

| feature pattern | type | meaning |
|---|---|---|
| `atk_role_dealer_prop_FULL`, `atk_role_tank_prop_FULL` | numeric | fraction of attacker strikers in each role |
| `def_role_dealer_prop_FULL`, `def_role_tank_prop_FULL` | numeric | fraction of defender strikers in each role |
| `atk_position_{front/middle/back}_prop_FULL` | numeric | attacker striker formation composition |
| `def_position_{front/middle/back}_prop_FULL` | numeric | defender striker formation composition |

---

## Interaction features: TTK summaries

### A) Strikers → strikers summaries

| feature pattern | type | meaning |
|---|---|---|
| `ttk_a*_to_def` | numeric | for attacker striker `ak`, mean TTK vs defender strikers `d1–d4` |
| `ttk_d*_to_atk` | numeric | for defender striker `dk`, mean TTK vs attacker strikers `a1–a4` |
| `ttk_avg_atk_to_def` | numeric | mean over all 16 attacker-striker → defender-striker pairs |
| `ttk_avg_def_to_atk` | numeric | mean over all 16 defender-striker → attacker-striker pairs |
| `ttk_delta_strikers` | numeric | `ttk_avg_def_to_atk - ttk_avg_atk_to_def` (positive = defender kills faster on average) |

### B) Specials → strikers summaries

Specials are evaluated only against opposing **strikers**. Specials that cannot deal direct damage are masked and excluded from averages.

| feature pattern | type | meaning |
|---|---|---|
| `ttk_as1_to_def_str`, `ttk_as2_to_def_str` | numeric | attacker special TTK vs defender strikers; nan if special cannot deal direct damage |
| `ttk_avg_atkS_to_def_str` | numeric | mean across attacker specials (damage-capable only) and defender strikers |
| `ttk_ds1_to_atk_str`, `ttk_ds2_to_atk_str` | numeric | defender special TTK vs attacker strikers; nan if special cannot deal direct damage |
| `ttk_avg_defS_to_atk_str` | numeric | mean across defender specials (damage-capable only) and attacker strikers |
| `ttk_delta_specials` | numeric | `ttk_avg_defS_to_atk_str - ttk_avg_atkS_to_def_str` |

---

## Notes on missingness

- Several **unit features** may be **nan** when a unit is flagged in a previous feature as not having given trait (e.g. heals self or group, etc.)
- **Special TTK features** (`ttk_as*_*`, `ttk_ds*_*`, and special averages/deltas) may be **nan** when a special is flagged as non-damaging (`{slot}_deals_dmg != "yes"`) and is therefore masked out.
- Some special slots (e.g., ds2, as2) lack certain merged attributes such as def_type or invul, depending on availability in source attribute tables and utility for training the model.
- All other engineered features are expected to be present given preprocessing pipeline and retained terrain context.
---