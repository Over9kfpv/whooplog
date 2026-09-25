---
title: Crafty — Snappier Horizon for Track Flying
description: Six Angle/Horizon settings changed for a tiny-whoop track, and an accelerometer calibration that had gone wrong.
lead: Aggressive on purpose. Saved, not yet flown when written.
date: 2026-09-13
weight: 10
toc: true
tags: ["tuning", "modes", "accelerometer"]
craft: ["Crafty"]
firmware: "Betaflight 2026.6.0-alpha (Apr 15 2026)"
duration: "Bench only — no flight"
log_file: ""
---

Only CLI `profile 2` (PID Profile 3 in the Configurator) was changed. Profiles 0 and 1 are
untouched, so switching profile brings back the old feel.

## How Horizon uses feedforward

Horizon has no feedforward setting of its own. It blends two setpoints:

- **Near centre stick**, self-levelling dominates. Its stick response is driven by
  `angle_feedforward`, with the tilt target scaled by `angle_limit`.
- **At larger deflection**, the acro part takes over and the normal `f_roll` / `f_pitch` /
  `f_yaw` feedforward applies.

`horizon_level_strength` sets how hard levelling pulls; `horizon_limit_sticks` sets how early it
fades as the stick moves.

## Changes

| Setting | Before | After | Why |
| --- | ---: | ---: | --- |
| `horizon_level_strength` | 75 | **35** | Less pull back to level, closer to acro |
| `horizon_limit_sticks` | 75 | **45** | Levelling fades out earlier with stick |
| `angle_feedforward` | 50 | **100** | Faster response in the levelling part |
| `feedforward_smooth_factor` | 65 | **45** | Less lag between stick and response |
| `feedforward_boost` | 15 | **20** | Sharper onset of each stick move |
| `angle_limit` | 60 | **70** | More tilt, so more speed in Angle and Horizon |

Rates and `throttle_limit_percent = 80` are shared across craft and were left alone. The throttle
scale is the biggest remaining limit on top speed.

## Accelerometer recalibration

After the change Horizon felt off. Sitting still on a level table, Crafty reported pitch past
+37° and roll past −15°. Two separate faults:

| Reading | Value | Meaning |
| --- | --- | --- |
| `acc_calibration` | `385,-492,-130` | Tilts a level craft ~14° roll and ~11° pitch |
| Accelerometer magnitude | 1.10 g | Far enough from 1 g that the IMU likely stopped trusting it |
| Gyro at rest | 5–7 °/s | Bad boot-time gyro calibration, integrated freely |

Subtracting the stored offsets gave a raw reading of almost exactly level, so the chip was fine
and the stored calibration wrong. The gyro reports as `ICM42622P`; the old offsets may predate it.
Fixed with `MSP_ACC_CALIBRATION` (code 205), level and still, then a save and reboot.

| | Before | After |
| --- | --- | --- |
| `acc_calibration` | `385,-492,-130` | `-41,43,-156` |
| Attitude at rest | pitch +37°, drifting | roll +0.1°, pitch −0.1° |
| Gyro at rest | 5–7 °/s | ~0 °/s |

{{< callout type="info" >}}
The same calibration works from the radio: disarmed, level and still, throttle high + yaw left +
pitch back calibrates the accelerometer; throttle low instead recalibrates the gyro. The gyro
also recalibrates on every battery plug-in.
{{< /callout >}}

## Test plan and rollback

Fly gently first: low level strength with high angle feedforward can overshoot when the stick
snaps to centre. If Horizon feels too loose, raise `horizon_level_strength` to 50 first.

```bash
python tools/bf_cli.py --save "profile 2" \
  "set horizon_level_strength = 75" "set horizon_limit_sticks = 75" \
  "set angle_feedforward = 50" "set feedforward_smooth_factor = 65" \
  "set feedforward_boost = 15" "set angle_limit = 60"
```
