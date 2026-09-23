---
title: Crafty — Replacement Board Restored and Recalibrated
description: The new board took the 17 September backup cleanly; the carried-over accelerometer calibration did not, and was redone.
lead: Same config, different physical board — so the level reference had to be taken again.
date: 2026-09-23
weight: 1
toc: true
tags: ["hardware", "accelerometer", "osd"]
craft: ["Crafty"]
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

**37 °C on USB, no battery — with a fan on the board.** Not a clean baseline.
[The last chase](/log/2026-09-17-crafty-temperature-warning/) is exactly why that caveat is
recorded rather than left implicit: the old board's 39–41 °C figures were fan-assisted too, and
that gap between fan and no-fan readings (up to 72 °C) is what sent the board away for
replacement in the first place. The real, no-fan baseline this board needs is still open.

## Blade orientation

Same as the Air65: default `QUADX` mixer, no custom `mmix`, `yaw_motors_reversed = ON` on both
craft. The restored config reproduces that, so prop rotation direction did not need to be
touched on the new board.

## Open before flying

- Confirm the motors are connected — the old board's last session left them unplugged for bench
  testing.
- Take one bench reading with **no fan** for a real thermal baseline.
- Fly one hover pack and check for the propwash-era level drift noted in the
  [13 September review](/log/2026-09-13-crafty-hover-and-crash-review/#in-the-hover-forward-stick-was-held-all-the-time).
