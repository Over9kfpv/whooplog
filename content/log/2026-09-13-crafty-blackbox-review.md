---
title: Crafty — Blackbox Review, Rougher After a Crash
description: Vibration roughly tripled after one crash, with normal RPM per command, which points at props or a shaft.
lead: The motors still produce normal output for their command. Something rotating is out of balance.
date: 2026-09-13
weight: 4
toc: true
tags: ["blackbox", "hardware", "telemetry"]
craft: ["Crafty"]
firmware: "Betaflight 2026.6.0-alpha (STM32G47X)"
duration: "39 logs, 29 armed with throttle"
log_file: "btfl_001.bbl … btfl_039.bbl"
---

The pilot reported Crafty "didn't sound very well" and some props looking worn. The flash held 39
logs and was completely full, so flights after `btfl_039` were not recorded.

| Parameter | Value |
| --- | --- |
| Logging | ~1 kHz, bidirectional DShot |
| Tune | `btfl_001`–`002` before, `btfl_003`–`039` after the [snappier Horizon](/log/2026-09-13-crafty-snappy-horizon/) change |
| Hover | ~40 % stick, ~21 000 RPM, 7–8 A |

## Vibration jumps after the `btfl_029` crash

`btfl_029` ends with the craft on its side (acc Z 0.47 g). The next two logs are failed takeoffs
where the mixer drops motor 2 to idle and drives motor 3 to full, the signature of a craft tipping
or snagged. From `btfl_032` the airframe is noisier. PIDs, rates and filters are identical, so the
change is physical.

Unfiltered gyro, roll and pitch averaged, throttle above 15 %:

| Log | 20–100 Hz | 100–250 Hz | 250–500 Hz |
| --- | ---: | ---: | ---: |
| `btfl_020` | 0.64 | 0.44 | 2.48 |
| `btfl_028` | 0.55 | 0.28 | 2.26 |
| `btfl_029` | 0.65 | 0.44 | 2.61 |
| `btfl_032` | 2.27 | 1.25 | 3.00 |
| `btfl_034` | 2.14 | 1.25 | 3.11 |
| `btfl_036` | 2.32 | 1.20 | 2.64 |
| `btfl_039` | 2.38 | 1.33 | 3.15 |

Filtered noise above 100 Hz rose from ~0.10 to ~0.33–0.37. The filters still work; there is more
to filter. Low-throttle and bench logs are excluded because idle RPM moves the bands.

## Motors 2 and 3 run rougher, but are not weaker

RPM jitter is the standard deviation of sample-to-sample eRPM change as a percentage of mean RPM,
in steady hover:

| Log | M1 | M2 | M3 | M4 |
| --- | ---: | ---: | ---: | ---: |
| `btfl_019` | 0.92 | 1.07 | 1.36 | 1.11 |
| `btfl_028` | 0.90 | 0.97 | 1.17 | 1.02 |
| `btfl_029` | 1.12 | 1.07 | 1.26 | 1.36 |
| `btfl_032` | 1.09 | **1.72** | **1.91** | 1.46 |
| `btfl_036` | 1.13 | **1.65** | **1.63** | 1.22 |
| `btfl_039` | 1.03 | **1.80** | **1.71** | 1.18 |

In Betaflight's default Quad-X numbering motor 2 is front right and motor 3 rear left. RPM per
unit of command, normalised to the mean, did **not** change across the crash (M3 ~0.94–0.97, M1
~1.05–1.09), so nothing is dragging or weak. Rough running with normal output points at the
rotating parts.

{{< callout type="info" >}}
The [later hover review](/log/2026-09-13-crafty-hover-and-crash-review/) revised this: motors 2
and 3 settled, and the lasting vibration is on motor 4.
{{< /callout >}}

## Not a finding: motor 2 "at 8 400 RPM" in `btfl_035`

The set script reports an 82 s flight with a −39 % front/rear split. In fact the craft sat armed
on the ground at zero throttle for ~72 s, airmode spinning the props and I-term winding up (pitch
129, roll 91), then took a throttle punch with that windup loaded. The averages describe ground
idle, not a motor fault.

{{< callout type="warning" >}}
Avoid long armed idles on the ground.
{{< /callout >}}

## Other observations

- **Battery sag:** resting packs read 3.8–4.2 V, but several flights dip to 3.10–3.17 V under load.
- **Current sensor:** the 1320 A full-scale glitch recurs in `btfl_019` and `btfl_035`.
- **Centre of gravity:** the front motors were 12.8 % faster on 12 Sep; clean hovers now sit
  between −2.7 % and +7.5 %.
- **Flash full:** the flight that sounded worst may not be in the set.

## Actions

1. Replace the props on motors 2 and 3, and any visibly worn ones.
2. With props off, spin motors 2 and 3 by hand: a wobbling bell means a bent shaft, a gritty feel a
   worn bearing.
3. Check the frame near those motors for cracks, and the FC mount and canopy for looseness.
4. Fly one pack in steady hover; 0.3–0.5 in the 100–250 Hz band means fixed.
