---
title: Crafty's Old Board — Cleaning Flux and Ink Stopped the Runaway Heating
description: The board pulled on 17 September overheated past 85 °C with no fan; after two IPA cleanings it levels off at 72 °C.
lead: Usable as a bench board, not yet flown. The residue was baked flux with marker ink dissolved in it.
date: 2026-09-25
weight: 1
toc: true
tags: ["hardware", "telemetry", "vtx", "safety"]
craft: ["Crafty"]
duration: "Bench only — no flight"
log_file: ""
---

The board pulled from Crafty after the [unresolved temperature warning](/log/2026-09-17-crafty-temperature-warning/)
went back on the bench to see whether it could still be used. With no airflow it overheated past
85 °C and was still climbing. After two isopropyl cleanings it levels off at 72 °C. It has not
been flown, and nothing was tested under battery or motor load.

{{< callout type="warning" >}}
**The VTX must not run without an antenna.** The bench had none fitted. The board boots the VTX
at 200 mW, so `vtx_power` was set to 1 (25 mW) and the AUX6 power mapping cleared, in RAM only.
That is not "off": Betaflight has no VTX off. Unplug the board between tests.
{{< /callout >}}

## The board

`BETAFPVG473_V2` on the BETAFPV dev firmware `2026.6.0-alpha`, `craft_name = Crafty`,
`vcd_video_system = AUTO`, `osd_displayport_device = MAX7456`, gyro **ICM42622P** at 8 kHz, 47 %
CPU. The gyro separates it from the [replacement board](/log/2026-09-23-crafty-board-replaced/),
which has a BMI270. The MCU ID was not read.

Photos showed the battery-lead pads (`+` and `–`) coated in black baked flux, with uneven solder
around the R001 shunt. The pink tint is **marker ink** used to colour parts, which dissolved into
the flux. It is not damage.

## Bench temperatures

USB only, no battery, no motors. Core temperature from `status`, sampled every 12–15 s.

| Condition | Core temperature |
| --- | --- |
| Not cleaned, no fan | 66 → 85 °C in ~2 min, still rising (stopped at the 85 °C cutoff) |
| Not cleaned, fan on, `pid_process_denom` 4 | 45 °C, flat |
| After first cleaning, no fan | 37 → 70 °C in ~85 s, then 74–75 °C for ~7 min |
| After second cleaning, no fan | 45 → 68 °C in 3.4 min, then 72 °C flat (73 °C last read) |
| Replacement board, no fan (23 Sep) | 63–65 °C at 3.5 min |

The 17 September bench reading of this board, a 72 °C plateau, matches the second-cleaning
result. Only the uncleaned run looks like a fault. Each cleaning lowered the plateau and slowed
the climb, which points at the residue, though the starting conditions differed from run to run.

Halving the PID rate (`pid_process_denom` 2 → 4) changed nothing: CPU stayed at 47 %. The 8 kHz
gyro loop cannot be lowered on this board.

## What is and is not known

**Known:** the runaway heating went away after cleaning. The board is stable with the fan on
(45 °C) and plateaus at 72 °C without it. Config, gyro and OSD are intact.

**Not known:**

- Whether flux and ink leakage was the whole cause. It is the likeliest reading, not proven.
- Which cleaning did what: both were done between runs.
- Current draw, motor outputs and DShot telemetry. No battery was connected.
- Flight behaviour. On 17 September the alarm tripped in flight at low VTX power.

72 °C is still above `osd_core_temp_alarm = 70`, so on the bench without airflow the warning
still fires.

## Outcome

Usable as a **bench and programming board**. Before flying it:

1. Fit a VTX antenna and restore the AUX6 power mapping.
2. Run a props-off battery test for current draw and all four motor outputs.
3. Fly a short pack and read the temperature on the OSD. If it warns, raise
   `osd_core_temp_alarm` (about 85) rather than chasing it. The replacement board's 63–65 °C
   shows the airframe is not the problem.

{{< callout type="info" >}}
IPA cleaning is the cheapest test for a board with heavy flux on it, and it should come before a
replacement is ordered. It changed the result here, though it is not proven to be the whole cause.
{{< /callout >}}
