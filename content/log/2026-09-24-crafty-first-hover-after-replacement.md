---
title: Crafty — First Hover on the Replacement Board, After Three Wrong Turns
description: A dead motor, a battery reading that looked worse than it was, and a board-alignment value pulled from the wrong file — each one produced an identical, violent flip.
lead: Stable in hand, then a clean hover, once the last of three stacked faults was found. Renamed Phoenix.
date: 2026-09-24
weight: 1
toc: true
tags: ["hardware", "accelerometer", "tuning", "safety"]
craft: ["Crafty"]
firmware: "Betaflight 2026.6.0-alpha (e92c10887, May 15 2026), BMI270"
duration: "~17 arms across the session; final hover 9–17 s each, 3 logs"
log_file: "btfl_001.bbl … btfl_003.bbl (final successful set)"
---

The [replacement board](/log/2026-09-23-crafty-board-replaced/) flipped on every single arm attempt,
regardless of what was fixed. Three genuine, unrelated faults were stacked on top of each other, and
each one alone was enough to cause the exact same violent flip. Clearing them one at a time, in the
wrong order, made each fix look like it had failed.

## 1. A dead motor, confirmed by RPM, not by hand

The rear-left motor spun freely by hand with no wobble — mechanically clean. Under actual DSHOT
drive, bidirectional telemetry told a different story:

| Motor | RPM at bench throttle |
| --- | ---: |
| 1, 3, 4 | 6 800 – 7 267 |
| **2 (rear-left)** | **233** |

A motor that spins freely unloaded but can't reach 4% of normal RPM under real drive current is an
electrical fault, not a mechanical one — a broken or cold phase-wire joint, not a bearing. Hand-spin
checks alone would have missed this entirely.

{{< callout type="info" >}}
`dshot_bidir` and `motor_pwm_protocol = DSHOT300` were off after the config reset that led to this
investigation. Turning bidirectional DShot back on is what made this diagnosis possible at all —
without RPM telemetry, "spins fine by hand" was the only data available, and it was wrong.
{{< /callout >}}

The motor was replaced. A direct RPM comparison afterward showed all four within a few percent
(6 667–7 167), confirming the physical fix — but the craft still flipped on every arm.

## 2. A battery that looked weak, but wasn't

Voltage in the logs sat at a flat 3.5 V before every flip, well below what a resting 1S pack should
show, with current spiking to 28–39 A at the moment of the flip. That looked like a dying pack
unable to supply real thrust demand.

{{< callout type="warning" >}}
It wasn't. This craft's [documented decoder bug](/log/2026-09-12-air75-blackbox-review/) makes
`blackbox_decode` under-report voltage by about 13%. 3.5 V in the CSV is really ~4.0 V — a healthy
pack. A fresh pack was swapped in anyway to be sure; the flip was identical. Battery health was never
the fault.
{{< /callout >}}

## 3. The board-alignment value, twice wrong in two different ways

Every flip showed the same signature: dead calm for several seconds of rising throttle, then one
single, massive rotation (1900–2450°/s) the instant real thrust built — never a gradual divergence.
That pattern doesn't match a bad motor or a weak battery; it matches the flight controller's whole
sense of its own orientation being wrong.

`align_board_yaw` had been left at its default `0` since the [clean config reset](/log/2026-09-23-crafty-board-replaced/).
A `.txt` CLI export was found online for what looked like the same physical unit
(`AIR75 F`), setting `align_board_yaw = -135`. It was applied, and the accelerometer recalibrated
against it — which read a perfect **0.0° level**.

{{< callout type="error" >}}
That "reads level" check proved nothing about whether the rotation itself was correct. Recalibrating
after setting a board-yaw value will always zero out the resting reading, true or not — calibration
compensates whatever bias exists *after* the rotation is applied. It cannot tell you the rotation was
right. The craft still flipped identically.
{{< /callout >}}

The file's `mcu_id` was then checked against the live board and **did not match** — a different
physical unit entirely. `align_board_yaw` was reverted to `0` and recalibrated fresh. The craft still
flipped, in the same way, on the very next arm.

### The value was right all along — a `mcu_id` mismatch isn't the same question

A second file for the same board and build (`0802 Freestyle GF 1614`) was loaded wholesale, `mcu_id`
mismatch and all, alongside a proper custom mixer table built by hand from
[Betaflight's own `mixerQuadX` source](https://github.com/betaflight/betaflight/blob/master/src/main/flight/mixer_init.c)
to correct what looked like a real motor-position/wiring mismatch, based on bench spin tests done
under the wrong (`0°`) alignment.

That custom mixer masked the problem just enough to pass single-motor bench tests, but not real
flight: every flip still showed one motor pinned at idle while two others maxed out, a diagonal
pattern that didn't match the axis the gyro actually reported. Reloading the second file's config
**as-is** — its own `align_board_yaw = -135`, the stock `QUADX` mixer, no custom `mmix` — fixed it.

{{< callout type="info" >}}
The lesson: `align_board_yaw` describes the PCB itself — how the gyro chip is soldered relative to
the frame reference — which is identical across every unit of one board model. `mcu_id` and
`acc_calibration` are per-unit and don't transfer. A `mcu_id` mismatch was the right reason for
caution the first time, but it doesn't invalidate a board-design constant the way it invalidates a
calibration offset. The custom `mmix` table, built to patch around the wrong alignment, was solving a
problem that the correct alignment didn't have.
{{< /callout >}}

## 4. Confirmed

Arm switch was reset to AUX4 (the file's own mapping used AUX1, which had no switch behind it on
this radio) and the accelerometer recalibrated once more under the correct alignment
(`acc_calibration = 26,-2,3,1`, level to 0.0°/-0.1°).

A hand-held test — held off the floor, throttle up, gently tilted — was stable. A short hover
followed, logged clean:

| Log | Duration | Motor balance | Gyro noise floor (roll/pitch/yaw) |
| --- | ---: | --- | --- |
| `btfl_001` | 16.9 s | −2.3 % to +3.7 % | 0.05 / 0.12 / 0.03 °/s |
| `btfl_002` | 9.6 s | −4.4 % to +4.0 % | 0.04 / 0.07 / 0.02 °/s |
| `btfl_003` | 13.2 s | −5.9 % to +3.1 % | 0.04 / 0.08 / 0.02 °/s |

No excursions in any of the three. This is the first genuinely stable data from the replacement
board.

## Renamed: Phoenix

`craft_name` read `AIR75 F` straight out of the loaded file — never `Crafty`. Rather than correct
it back, the board got a name of its own: **Phoenix**. Same lineage as Crafty (the replacement for
[the board that overheated](/log/2026-09-17-crafty-temperature-warning/)), but a fresh physical unit
that earned a fresh name after flying on the fourth attempt to get it right.

{{< callout type="info" >}}
This log entry and the site's `craft` taxonomy still say **Crafty**, for continuity with the
existing `/craft/crafty/` history. `craft_name` on the board itself says **Phoenix**. If the OSD name
and the site tag drift apart in practice, worth revisiting.
{{< /callout >}}

## Brought in line with the fleet standard

The loaded file covered the tune and the fix, but not [the pilot's own switch layout, throttle curve
and crashflip settings](/reference/pilot-preferences/) — those were never part of it, and the earlier
full defaults reset had wiped them. Checked against that reference page and corrected:

| Setting | Was | Now |
| --- | --- | --- |
| `vcd_video_system` | `NTSC` | **`AUTO`** — a real bug, not a preference; NTSC is Air65-only |
| `feature TELEMETRY` | off | on |
| ARM range | `1700`–`2100` | `1800`–`2100` (deliberately narrow, matches the fleet) |
| BEEPER channel | AUX4 (doubled with arm) | AUX1 |
| CRASHFLIP channel | AUX3 | AUX5 |
| OSD profile adjustment | unset | AUX3, adjustment 29 |
| `crashflip_rate` | `0` (crashflip did nothing) | `30` |
| `throttle_limit_percent` | `100` | `80` |
| `thr_mid` / `thr_expo` / `thr_hover` | `50` / `0` / `50` | `40` / `35` / `44` |
| `fpv_mix_degrees` | `0` | `10` |
| `vtx_low_power_disarm` | `OFF` | `ON` |
| `pilot_name` | unset | `HansF` |

`crashflip_motor_percent = 0` and `crashflip_auto_rearm = OFF` were already correct — matching this
site's own [crashflip notes](/reference/crashflip/), not an oversight.

## Final configuration

| Setting | Value |
| --- | --- |
| `craft_name` | `Phoenix` |
| `align_board_yaw` | `-135` |
| `yaw_motors_reversed` | `ON` |
| `mixer` | `QUADX` (stock — no custom `mmix`) |
| `dshot_bidir` | `ON`, `motor_pwm_protocol = DSHOT300` |
| PID profile | `GF 1614`, tuned for `0802` motors (p/i/d/f 33/59/21/35 pitch, 33/60/21/35 roll) |
| Rates | `roll_expo/pitch_expo/yaw_expo = 10/10/5`, `srate = 75/75/70` |
| `acc_calibration` | `26,-2,3,1` |
| ARM | AUX4, `1800`–`2100` |
| Crashflip | AUX5, `1700`–`2100`, `crashflip_rate = 30` |
| Throttle | `throttle_limit_percent = 80`, `thr_mid/expo/hover = 40/35/44` |

## Open items

- Rear-left motor was replaced on the bench; no spare tracking or part number recorded here yet.
- The rate/expo values are noticeably gentler than Crafty's older `GF 1614` tune on the previous
  board (`srate 55`, `expo 35`) — this file's values are specific to the `0802` motors, not carried
  over. Worth flying more before touching them.
