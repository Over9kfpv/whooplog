---
title: Crafty — Overheating After a Crash, Not From Flying
description: The OSD temperature warning followed ground re-arms against a jammed prop, not flight.
lead: Motor 4 was commanded to 100 % while turning at 200–400 RPM.
date: 2026-09-16
weight: 4
toc: true
tags: ["blackbox", "hardware", "telemetry"]
craft: ["Crafty"]
duration: "9 logs, 2 packs"
log_file: "btfl_001.bbl … btfl_009.bbl"
---

## Which temperature

| Setting | Value | Meaning |
| --- | --- | --- |
| `osd_core_temp_alarm` | 70 | The warning is the MCU core temperature |
| `osd_esc_temp_alarm` | 0 | No ESC temperature alarm |
| `dshot_edt` | OFF | ESC and motor temperature are not reported at all |
| `vtx_power` | 1 (25 mW) | Lowest power; not the source |

On this AIO board the MCU shares the PCB with the ESC FETs, so a fast core rise in flight
usually means ESC heat.

## Session

| Log | Pack | Armed | What happened |
| --- | --- | ---: | --- |
| `btfl_002` | 1 | 157 s | Normal flight, crash at the end |
| `btfl_003` | 1 | 2 s | Re-arm, takeoff, crash |
| `btfl_004` | 1 | 16 s | Normal flight |
| `btfl_005` | 2 | 21 s | Flight ending upside down (acc Z −0.94 g) |
| `btfl_006` | 2 | 0.8 s | Ground arm at zero throttle, 25–30 A bursts |
| `btfl_007` | 2 | 1.6 s | Ground arm, motor 3 held at minimum |
| `btfl_008` | 2 | 3.0 s | Ground arm at zero throttle, 23–29 A bursts |
| `btfl_009` | 2 | 1.2 s | **Motor 4 commanded to 100 % while stalled** |

## Finding

In `btfl_009` motor 4 (front left) sits at maximum output (`2047`) for about 0.8 s while turning
at **~200–400 RPM**. A blocked prop draws stall current and almost all of it becomes heat.
`btfl_006` and `btfl_008` add ground arms at zero throttle with **23–30 A** total, against
7.5 A in hover. These few seconds explain a fast rise to 70 °C.

## Healthy: the prop fix worked

| | 13 Sep `btfl_001` | 16 Sep `btfl_002` | 16 Sep `btfl_004` |
| --- | ---: | ---: | ---: |
| Hover current | ~7.6 A | 7.5 A | 7.8 A |
| Motor 4 once-per-rev vibration | 34.2 | 14.7 | 9.0 |

Motor 4 now needs 1–3 % more command per RPM than on 13 September, inside the 3 % healthy
spread but worth watching.

{{< callout type="warning" >}}
Disarm the moment the craft crashes and free the props by hand. If a prop does not turn in
turtle mode, stop: repeated arms against a jammed prop heat the ESC and can burn the windings.
{{< /callout >}}
