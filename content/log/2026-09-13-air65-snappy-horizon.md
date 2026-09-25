---
title: Air65 — Crafty's Snappier Horizon Copied Over
description: The same six Angle/Horizon changes, applied to profile 0, with the craft-specific settings left out.
lead: Saved, not yet flown when written.
date: 2026-09-13
weight: 9
toc: true
tags: ["tuning", "modes"]
craft: ["Air65"]
duration: "Bench only — no flight"
log_file: ""
---

The six changes from [Crafty's snappier Horizon](/log/2026-09-13-crafty-snappy-horizon/) were
applied to the Air65. Crafty flies CLI `profile 2`; the Air65 flies `profile 0` (`GF 1219S`,
tuned for its motors), so the settings went into **profile 0**. Its other profiles are untouched.

| Setting | Before | After |
| --- | ---: | ---: |
| `horizon_level_strength` | 75 | **35** |
| `horizon_limit_sticks` | 75 | **45** |
| `angle_feedforward` | 50 | **100** |
| `feedforward_smooth_factor` | 65 | **45** |
| `feedforward_boost` | 15 | **20** |
| `angle_limit` | 60 | **70** |

All six read back correctly after the reboot, with `profile 0` still active.

## Deliberately not copied

- **Per-axis feedforward.** Crafty runs 120/125/120; the Air65 keeps its motor-specific values
  (`f_pitch 41`).
- **Accelerometer calibration.** It is specific to each board, and the Air65 had its own
  [level-reference problem](/log/2026-09-13-air65-takeoff-pitch-up/).

{{< callout type="warning" >}}
The pre-change backup was taken while an unsaved read had switched the board to profile 2 in
memory, so it records `profile 2` as active. After restoring it, run `profile 0` and `save`.
{{< /callout >}}

Rollback:

```bash
python tools/bf_cli.py --save "profile 0" \
  "set horizon_level_strength = 75" "set horizon_limit_sticks = 75" \
  "set angle_feedforward = 50" "set feedforward_smooth_factor = 65" \
  "set feedforward_boost = 15" "set angle_limit = 60"
```
