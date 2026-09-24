---
title: BETAFPV G473 V2 — 1S Whoop Build Spec
description: Board, MCU, sensors, storage and firmware for the airframe documented here.
lead: The hardware every other page on this site refers to.
weight: 1
toc: true
craft: ["Crafty"]
---

{{< badge content="Betaflight 2026.6.0-alpha" >}}
{{< badge content="MSP API 1.48" >}}

## Flight controller

| Item | Value |
| --- | --- |
| Board target | `BETAFPVG473_V2` |
| Manufacturer ID | `BEFH` |
| MCU | STM32G474, 168 MHz (PLLR-HSE) |
| Gyro / accelerometer | ICM42622P (SPI, DMA, locked) |
| OSD | MAX7456, 30 × 13 characters |
| Onboard flash | 16 MB SPI (JEDEC `0x00852018`) — used for blackbox |
| Detected buses | SPI × 1, I²C × 0 |
| Power | 1S |

Reported gyro rate in normal operation is ~8 kHz with a cycle time around 123–128 µs and
roughly 47% CPU load.

## Firmware

```text
Betaflight / STM32G47X (G473) 2026.6.0-alpha
MSP API: 1.48
```

{{< callout type="warning" >}}
This is an **alpha** build. Most MSP binary reads do not respond on it — see
[Driving Betaflight from Claude Code](/docs/betaflight-mcp-claude-code/) for the specific
message IDs that time out and why CLI text is the reliable transport here.
{{< /callout >}}

## What is not on this board

No GPS, no magnetometer, no barometer detected. This matters in two places:

- GPS Rescue is unavailable, so failsafe is a drop-or-land decision only.
- Blackbox logs contain **no location data**, which is why they are safe to share or
  discuss openly. A GPS-equipped craft would need home-point coordinates scrubbed before
  any log left the machine.

## Replacement board

This board was replaced on 23 September, after a temperature warning that three separate
investigations never isolated (see the [flight log entry](/log/2026-09-17-crafty-temperature-warning/)).
Everything under **Flight controller** above describes the airframe as it flew through
17 September, on the board that's now retired.

The replacement was ordered as a `BETAFPV Matrix 5-in-1 V2 AIO, G473, 12 A, 1S` — listed as
`Matrix 1S Brushless Flight Controller (5IN1 II)` at most other retailers — and arrived on the
same `BETAFPVG473_V2` target with a different gyro substitute:

| Item | Old board | New board |
| --- | --- | --- |
| MCU ID | *(retired, not recorded)* | `003400395346501620373142` |
| Gyro / accelerometer | ICM42622P | **BMI270** |
| Firmware | Betaflight 2026.6.0-alpha | Betaflight 2026.6.0-alpha (`e92c10887`) |

The new board isn't a continuation of this one. It took its own share of setup faults — a dead
motor, a battery reading that looked worse than it was, and a board-alignment value that was
wrong in two different ways before it was right — and flies under its own name now:
**[Phoenix](/craft/phoenix/)**. The full story is on
[the blog](/blog/phoenix-rises-a-boards-hard-week/); its own settings and findings are tracked
under [`craft: Phoenix`](/craft/phoenix/), not here.

## Known sensor issue

The current sensor on this board reports implausible values — see the
[flight log entry](/log/2026-09-08-post-rebuild-shakedown/) for a 1320 A reading that is an
ADC scaling artifact rather than a real event. Treat `amperage` columns in the blackbox
data as untrustworthy until `ibata_scale` and `ibata_offset` are recalibrated.
