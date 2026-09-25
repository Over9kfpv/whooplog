---
title: Crafty — Blackbox Review of an 18-Arm Session
description: A healthy tune, a forward centre of gravity, and a decoder bug that made a good pack look sick.
lead: Logged under the name AIR75 F, from the same board as Crafty.
date: 2026-09-12
weight: 11
toc: true
tags: ["blackbox", "tuning", "hardware", "telemetry"]
craft: ["Crafty"]
firmware: "Betaflight 2026.6.0-alpha (STM32G47X)"
duration: "434 s armed, 18 arms, 13 flights"
log_file: "btfl_001.bbl … btfl_018.bbl"
---

Eighteen arms on one continuous power cycle; thirteen left the ground and five were bench
arms. The tune is healthy. Two hardware findings are not.

{{< callout type="info" >}}
Every log carries `DeviceUID 002700443235511330303938`, the same flight controller the backups
record as Crafty. The board was logging as `AIR75 F` at the time, so those names refer to one
drone. It is filed under Crafty here.
{{< /callout >}}

## Session

| Parameter | Value |
| --- | --- |
| Logging | 1 kHz effective, 1/4 decimation, bidirectional DShot |
| Arms / flights | 18 / 13 |
| Hover throttle | ~35 % stick, `throttle_limit_percent = 75` |
| Mean hover RPM | ~20 000 |

The only configuration change is `throttle_limit_percent` 100 → 75 between `btfl_004` and
`btfl_005`. PIDs, rates and filters are identical across all 18 logs, so every trend is physical.

## The centre of gravity sits forward

The two front motors hold **12.8 % more RPM than the rear pair** in every level flight, while
left against right stays within 1.4 %. The pitch I-term parks at **−27** rather than averaging
to zero. Two independent measurements, one from the mixer and one from the controller, agree.

| Log | Front RPM | Rear RPM | Split | I-term pitch |
| --- | ---: | ---: | ---: | ---: |
| `btfl_006` | 21 453 | 18 837 | +13.0 % | −25.5 |
| `btfl_009` | 21 439 | 18 708 | +13.6 % | −27.6 |
| `btfl_012` | 21 595 | 18 397 | +16.0 % | −34.3 |
| `btfl_015` | 21 205 | 18 915 | +11.4 % | −23.9 |
| `btfl_018` | 20 671 | 18 353 | +11.9 % | −24.2 |

Thrust goes with RPM squared, so 12.8 % is about 27 % more thrust on the front pair, or a centre
of gravity roughly 3 mm ahead of centre. Moving the pack back a few millimetres and refitting a
hover should settle the pitch I-term nearer zero.

## The voltage readings were the decoder, not the sensor

`blackbox_decode` treats the `vbatLatest` field as a raw sensor count and divides by
`vbat_scale`, but Betaflight 2026.6 already logs it in hundredths of a volt. Every printed
voltage is low by about 1.13.

| Source | Value |
| --- | ---: |
| Raw logged field (`--unit-vbat raw`) | `419` |
| Header `vbatref` | `421` |
| True voltage | **4.19 V** |
| OSD | 4.2 V |
| CSV as written by the decoder | **3.714 V** |

```bash
blackbox_decode --unit-vbat raw --stdout btfl_009.bbl | head -3
```

{{< callout type="warning" >}}
`vbat_scale = 113` is correct. An earlier reading of this data concluded it under-read by 15 %.
That was wrong.
{{< /callout >}}

Corrected, one pack runs from 4.11 V to 3.63 V over ten minutes, with in-flight minima to 3.09 V
under punches. Ordinary 1S behaviour.

## The current sensor pins to full scale

`btfl_003`, `005`, `013` and `016` each contain a reading of **1320.2 A**, which also corrupts
the mAh counter (one log claims 1360 mAh). Amperage is unusable in those four logs. The rest show
a sane 4–5 A hover and about 15 mΩ of pack-plus-wiring resistance.

## Healthy: filters and tracking

- **Noise floor:** the only significant peak is the motor fundamental at 315–325 Hz, exactly
  20 000 RPM across 12 poles. The RPM filter and dynamic notch remove **21×** of it.
- **D-term:** RMS near 5 in calm flight. High figures are confined to the last second of a log.
- **Tracking:** setpoint-to-gyro error of 3–5 °/s on calm flights.

## Impacts

Eight of thirteen flights peak above 4 g, six ending within 1.5 s of that peak. Noise floor and
motor balance are unchanged after every impact, so no prop cracked and no motor bell shifted.

## Loose ends

- `motor_kv = 1960` cannot produce 20 000 RPM on one cell. Harmless unless an RPM-limit feature
  is used.
- `throttle_limit_percent` was 75 here against the 80 % preference.
