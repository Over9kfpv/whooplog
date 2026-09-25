---
title: Air65 — The Nose-Up Takeoff, Traced to the Level Reference
description: The accelerometer reference was off by 6.5° pitch for six arms and correct for the twenty-four before.
lead: The craft tilted back as it left the ground. The airframe was fine; the level reference was not.
date: 2026-09-13
weight: 8
toc: true
tags: ["blackbox", "tuning", "accelerometer", "safety"]
craft: ["Air65"]
firmware: "Betaflight 2026.6.0-alpha (e92c10887, May 15 2026)"
duration: "31 logs, 26 with flight, 8 power cycles"
log_file: "air65f_0913_btfl_001.bbl … air65f_0913_btfl_031.bbl"
---

## Finding

On the last few launches the craft tilted back as it lifted off, and a physical
inspection found nothing. The logs show the pitch level reference stepped by about **6.5°**
between pack 6 and pack 7 and stayed there. Angle mode held a standing pitch correction while
the craft sat on the ground, and it released into the airframe when the craft broke ground.

## The fault

With the craft armed, stationary and sticks centred, the accelerometer should read level:

| Logs | Ground pitch | Ground roll |
| --- | ---: | ---: |
| `btfl_001` … `btfl_024` (packs 1–6) | −0.9° … +0.9° | ~0° |
| `btfl_025` | +6.11° | −0.06° |
| `btfl_026` | +6.19° | +0.00° |
| `btfl_027` | +6.50° | +0.00° |
| `btfl_028` | +6.50° | −0.14° |
| `btfl_029` | +7.55° | −0.92° |
| `btfl_030` | +6.31° | −0.11° |

Pitch only; roll is untouched. The step lands on a power-cycle boundary and persists across a
further one. The gyro is not implicated: it reads exactly `0.00` on all axes whenever the craft
is stationary.

## Why that tilts the craft

Angle mode does not wait for takeoff. Believing the craft to be tilted, it corrects while still
on the ground, and with `pid_at_min_throttle = 1` the correction reaches the motors at idle:

| Log | rear-R | front-R | rear-L | front-L | Split |
| --- | ---: | ---: | ---: | ---: | ---: |
| `btfl_025` | 3 017 | 10 011 | 3 274 | 10 022 | +104 % |
| `btfl_026` | 2 996 | 9 960 | 3 153 | 9 858 | +105 % |
| `btfl_029` | 2 982 | 9 592 | 4 011 | 10 547 | +97 % |

Before log 025 that split is within a few percent. Raising throttle carries the asymmetry up the
ramp: 50 ms before liftoff in `btfl_030` the motors read 7 150 / 19 233 / 7 467 / 19 800 RPM.
Thrust goes with RPM squared, so that is roughly seven times the thrust on one end of the
airframe at the moment it becomes free to rotate.

## What is not wrong

- **Gyro:** zero bias on all axes when stationary, in all 31 logs.
- **Roll axis:** ground roll within 1°; left/right balance normal.
- **Hover balance:** front/rear split settles to about 3 %, against 2.3 % in the earlier review.
- **Motors:** RPM per unit of command within ~2 % across all four in every clean flight.

## Fix

The accelerometer was recalibrated on a level surface over USB and saved.

| | `acc_calibration` | Attitude on a level surface |
| --- | --- | --- |
| Before | `-2,-7,36,1` | pitch +5.0°, roll +0.0° |
| After | `-179,-8,-25,1` | pitch −0.8°, roll −0.4° |

Verified stationary for 90 s (0.84° of drift, no stepping) and again after a reboot.
`acc_trim_pitch` and both `align_board_*` values were 0 throughout; only the calibration
offsets changed.

{{< callout type="warning" >}}
**Root cause is not settled.** The data does not separate a wrong stored calibration from a
craft that really was resting 6.5° nose-up on its launch surface. The evidence leans to the
calibration — a pitch-only offset, constant across six arms and two power-ups — but it is not
proof. The check: the first second of the next session's log. Ground pitch near zero and a small
idle front/rear split means fixed; 6.5° returning on a known-level surface means the board.
{{< /callout >}}

{{< callout type="info" >}}
Betaflight reports attitude pitch with the **opposite sign** to `asin(accX / 1G)`. Verified live
against `MSP_ATTITUDE`: accX −175 counts reads as pitch +5.0°.
{{< /callout >}}

## Housekeeping

- The flash was full (16 777 216 bytes) when pulled, so logging had already stopped. Erased.
- `motor_kv = 1960` is still wrong against ~27 000 RPM observed on one cell. Harmless.
- `dyn_notch_max_hz = 400` still sits below the ~455 Hz motor fundamental.
