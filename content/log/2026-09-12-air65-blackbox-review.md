---
title: Air65 — Blackbox Review of 16 Logs
description: A balanced airframe, a log that samples below its own motor frequency, and crashes that hid the tune.
lead: Fourteen flights across eight packs. The statistics were measuring collisions.
date: 2026-09-12
weight: 12
toc: true
tags: ["blackbox", "tuning", "hardware", "telemetry"]
craft: ["Air65"]
firmware: "Betaflight 2026.6.0-alpha (e92c10887, May 15 2026)"
duration: "8 m 38 s armed, 14 flights, 8 packs"
log_file: "air65f_btfl_001.bbl … air65f_btfl_016.bbl"
---

Pulled off a completely full 16 MB flash. Sixteen logs, fifteen readable, fourteen with flight.
The flight-controller clock restarts in each pack, so this is not one session. Device UID
`002900023235510535303333` identifies the board.

| Parameter | Value |
| --- | --- |
| Gyro loop | 3.2 kHz (`looptime 312`, `pid_process_denom 1`) |
| Logging | **811 Hz**, 1/4 decimation |
| Hover | ~43 % stick against `thr_hover = 44` |
| Mean hover RPM | ~27 000, a **455 Hz** fundamental |
| Throttle limit | 80 % |

## Healthy: the airframe is balanced

Front against rear averages **2.3 %** and changes sign between flights, which no standing bias
does. Pitch I-term averages +1.1 and crosses zero freely.

| Metric | Air65 | Crafty (12 Sep) |
| --- | ---: | ---: |
| Front/rear RPM split | 2.3 % (sign varies) | 12.8 % (always front) |
| Pitch I-term, mean | +1.1 | −26.6 |
| Left/right RPM split | +2.4 % | +0.7 % |
| Roll I-term, mean | +5.6 | +1.4 |

The one small asymmetry is roll: left runs 2.4 % faster in thirteen of fourteen flights. A slight
lean to the right, an order of magnitude below the Crafty pitch problem.

## The blackbox samples below its own motor frequency

A 455 Hz fundamental against an 811 Hz log gives a Nyquist limit of 406 Hz, so everything above
folds back. A real motor peak moves with RPM; this one pins near 400 Hz regardless:

| Log | Mean RPM | True fundamental | Apparent peak |
| --- | ---: | ---: | ---: |
| `btfl_004` | 26 752 | 446 Hz | 393 Hz |
| `btfl_005` | 26 836 | 447 Hz | 400 Hz |
| `btfl_013` | 26 907 | 448 Hz | 394 Hz |

{{< callout type="warning" >}}
This is a logging limitation only; the controller filters at the full 3.2 kHz. But these logs
**cannot be used to judge filter performance**, and the 5.7–9× attenuation figure is not
comparable to Crafty's 22×. To tune filters from logs on this craft, lower the decimation:
`blackbox_sample_rate` 1/2 gives ~1.6 kHz, 1/1 the full 3.2 kHz.
{{< /callout >}}

## `dyn_notch_max_hz` sits below the fundamental

It is **400**, under the 455 Hz the motors produce, so the dynamic notch never reaches the
fundamental. The RPM filter does (bidirectional DShot, 3 harmonics), so this is lost redundancy
rather than a fault. Betaflight's default is 600.

## Voltages: the decoder, again

The same `blackbox_decode` scaling bug as in the
[Crafty review](/log/2026-09-12-air75-blackbox-review/) applies: CSV volts read 12.8 % low.
Corrected, four logs open on a fresh pack at 4.19–4.20 V, each pack works down to about 3.7 V
before a swap, and in-flight minima reach 2.88 V under hard punches. `vbat_scale = 113` is
correct on both craft.

## The crashes were hiding the tune

Nine of fourteen flights end within 1.5 s of their acceleration peak, with peaks to **13.8 g**.
Masking one second either side of every sample above 4 g changes the picture:

| Metric | Raw | Impacts masked |
| --- | ---: | ---: |
| Tracking error, roll | ~60 °/s | 5 °/s |
| Tracking error, pitch | ~55 °/s | 9 °/s |
| D-term RMS, roll | 93 | 19 |

Noise floor and motor balance are unchanged after every impact, so nothing broke.

## Open items

- `motor_kv = 1960` here too, against ~27 000 RPM observed. Harmless.
- `btfl_003` is a 6 kB fragment with no timing. Not recoverable.
- The flash was completely full (16 777 216 of 16 777 216 bytes), so logging had stopped.
