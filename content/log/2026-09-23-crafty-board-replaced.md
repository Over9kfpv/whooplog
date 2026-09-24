---
title: Phoenix — Replacement Board Restored and Recalibrated
description: The new board took the 17 September backup cleanly; the carried-over accelerometer calibration did not, and was redone.
lead: Same config, different physical board — recalibrated, baselined, ready to fly. Not yet named Phoenix at this point; see the 24 September entry.
date: 2026-09-23
weight: 2
toc: true
tags: ["hardware", "accelerometer", "osd"]
craft: ["Phoenix"]
duration: "Bench only — no flight"
log_file: ""
---

The replacement ordered after the
[unresolved temperature warning](/log/2026-09-17-crafty-temperature-warning/) arrived and was
fitted. It reports the same `BETAFPVG473_V2` target, already on the correct BETAFPV dev
firmware (`e92c10887`, matching the pre-replace backup) with no reflash needed. The gyro on
this unit is `BMI270`, one of the substitutes the board ships with when `ICM42688` is scarce —
see [the gyro firmware notes](/reference/betafpv-gyro-firmware/).

## Restore

Config restored from `backup_2026-09-17_195000_crafty_prereplace.txt`, taken the day the old
board was pulled. Verified after the reboot:

| Setting | Value |
| --- | --- |
| `craft_name` | `Crafty` |
| `vcd_video_system` | `AUTO` — correct for this craft |
| `osd_displayport_device` | `MAX7456` |
| Crashflip | AUX5, mode 35, `crashflip_rate = 30`, `crashflip_auto_rearm = OFF` |
| PID profile 0 | `GF 1614`, unchanged |
| PID profile 2 | the [snappier Horizon](/log/2026-09-13-crafty-snappy-horizon/) settings |

## The carried-over calibration was wrong

`acc_calibration` restored at `-63,17,-35,1` — the old board's offsets, replayed onto a
different physical chip. That value is a property of one sensor's mounting and manufacturing
tolerance, not of the airframe, so it does not transfer across a board swap even though
everything else in the backup does.

Recalibrated level and still, over MSP (`MSP_ACC_CALIBRATION`, code 205):

| | Old board | New board |
| --- | --- | --- |
| `acc_calibration` | `-63,17,-35,1` | `-4,-44,-15,1` |
| Attitude at rest | — | roll 0.0°, pitch 0.0° |

{{< callout type="warning" >}}
A config backup restores everything **except** the accelerometer calibration correctly after a
board swap. Recalibrate on a level surface before the first flight on new hardware, even when
restoring an otherwise-known-good config.
{{< /callout >}}

## Bench temperature

A first reading, **37 °C on USB with a fan on the board**, was not a usable baseline — that is
exactly the confound that sent the old board away in the first place. A no-fan reading followed
it, tracked over time rather than as a single number, since the
[previous investigation](/log/2026-09-17-crafty-temperature-warning/) found no-fan readings keep
climbing for minutes before flattening:

| Time | Core temperature |
| --- | ---: |
| 30 s | 42 °C |
| 60 s | 49 °C |
| 90 s | 54 °C |
| 120 s | 57 °C |
| 150 s | 63 °C |
| 180 s | 64 °C |
| 210 s | 65 °C |

Levelling off around 63–65 °C at 3.5 minutes, USB only, no battery. That lands close to the
Air65 control reading from the same investigation (36 → 60 °C at 3 minutes, still rising), and
well under the old board's 70–72 °C plateau. This is the real baseline for the new board.

## Blade orientation

Same as the Air65: default `QUADX` mixer, no custom `mmix`, `yaw_motors_reversed = ON` on both
craft. The restored config reproduces that, so prop rotation direction did not need to be
touched on the new board.

## Status

Motors confirmed connected. Config, blade orientation, calibration and a clean thermal baseline
are all in place — the board is ready to fly.

**Still open:** watch for the propwash-era level drift noted in the
[13 September review](/log/2026-09-13-crafty-hover-and-crash-review/#in-the-hover-forward-stick-was-held-all-the-time)
on the first hover pack, and confirm the core temperature stays near this baseline in flight
rather than repeating the 09-17 warning.
