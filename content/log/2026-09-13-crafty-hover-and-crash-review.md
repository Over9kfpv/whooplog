---
title: Crafty — Hover Pack, Crash Pack and the Odd Sound
description: A once-per-revolution vibration on the front-left motor, matched between gyro and audio, and a level reference that drifts in the air.
lead: The sound is motor 4, which was visibly tilted forward after the crash.
date: 2026-09-13
weight: 3
toc: true
tags: ["blackbox", "hardware", "telemetry", "modes"]
craft: ["Crafty"]
duration: "5 logs, 2 packs, plus flight audio"
log_file: "btfl_001.bbl … btfl_005.bbl"
---

Two packs flown after the [morning review](/log/2026-09-13-crafty-blackbox-review/). Pack 1 was a
deliberate steady hover; pack 2 was flown actively, crashed twice and needed a turtle flip. The
pilot also recorded flight audio because of an odd sound. The pilot calls this airframe the AIR75;
it reports `craft_name = Crafty` and Crafty's `DeviceUID`, so it is the same drone.

The flash was full again: `btfl_005` stops at 52 s with no clean end.

| Log | Pack | Armed | What happened |
| --- | --- | ---: | --- |
| `btfl_001` | 1 | 203 s | Steady hover, one pack |
| `btfl_002` | 2 | 5 s | Short hop, bump at 4.5 s |
| `btfl_003` | 2 | 17 s | Turning flight, ends in a crash |
| *21 s gap* | 2 | | Same power cycle, nothing recorded; most likely the turtle flip |
| `btfl_004` | 2 | 156 s | Active flight, ends in a crash (7.5 g) |
| `btfl_005` | 2 | 52 s+ | Rearmed 2.4 s later; logging stopped when the flash filled |

All five logs share one tune, flown in Horizon. Pack 1 bottomed at 3.52 V, pack 2 at 3.20 V.

## The sound is a once-per-revolution vibration, loudest on motor 4

The recording lines up with the logs to within ~0.1 s, so each motor's measured RPM can be matched
to the audio. Crafty flies 3-blade props, so a healthy whoop sounds mostly like blade pass
(~1 050–1 100 Hz) plus electrical whine at 6× rotation (~2 100 Hz). This recording is dominated by
**1× rotation, ~335–370 Hz**, the tone of an out-of-balance spinning part.

Audio tone prominence, dB above the surrounding noise, at each motor's own harmonics:

| Harmonic | M1 | M2 | M3 | M4 |
| --- | ---: | ---: | ---: | ---: |
| 1× (imbalance), `btfl_004` | 14.4 | 12.8 | 12.8 | **17.3** |
| 1× (imbalance), `btfl_005` | 18.8 | 14.5 | 13.6 | **19.5** |
| 3× (blade pass), `btfl_004` | 4.1 | 6.1 | 3.6 | 4.5 |

The gyro agrees, and has history. Unfiltered roll and pitch gyro demodulated at each motor's exact
rotation phase in steady flight (background ~1–4):

| Log | M1 | M2 | M3 | M4 |
| --- | ---: | ---: | ---: | ---: |
| `btfl_009` (12 Sep) | 8.6 | 4.1 | 8.1 | 12.6 |
| `btfl_028` (before the crash) | 6.9 | 3.9 | 8.0 | 16.5 |
| `btfl_036` (after the crash) | 6.4 | 4.9 | 5.5 | **27.3** |
| `btfl_039` | 6.8 | 3.1 | 9.9 | **41.7** |
| `btfl_001` (hover) | 3.5 | 6.4 | 3.8 | **34.2** |
| `btfl_004` | 7.8 | 2.8 | 7.0 | **27.7** |
| `btfl_005` | 14.7 | 3.9 | 4.8 | **23.3** |

In the hover log the strongest gyro peak between 250 and 450 Hz sits at 369–373 Hz, exactly motor
4's rotation speed. Motor 4 is **front left**. Its imbalance roughly doubled at the morning crash
and has not recovered, while its RPM per command is normal (1.005), so it is not dragging. Motors
2 and 3 have settled.

### The rattle

The pilot describes a rattle, not a hum. In the 2.5–9 kHz band there is no steady clicking (about
30 separate clicks in 214 s, mostly handling), and click energy at each motor's rotation rate sits
only ~2 dB above background for all four. The roughness follows loudness (r = +0.60) and only
faintly follows motor 4's vibration (r = +0.14 with loudness removed). The recording is HE-AAC at
48 kbit/s, which smears clicks, so audio alone cannot locate it. The best fit is a loose part
shaken by motor 4's imbalance, such as a prop worn loose on its shaft.

### The front-left motor is visibly tilted

On inspection motor 4 leans forward a little, which fits its imbalance doubling at the `btfl_029`
crash. A few degrees of lean tips only about 2 % of the craft's weight sideways, and steady-hover
yaw I-term and diagonal mix wander between −1.7 % and +1.6 % with no step at the crash. The tilt
therefore matters through vibration and a possibly loose mount, not through a steady push.

{{< callout type="warning" >}}
**Actions.** With the battery out, push the front-left motor side to side: movement or a click
means a cracked mount or loose screws. Replace the frame if the plastic is bent, since it seldom
stays straight. Fit a new prop, spin the bell with the prop off to check the shaft, and wiggle
every prop for play. Then fly one hover: a fix shows motor 4 below ~15 in the table above.
{{< /callout >}}

## In the hover, forward stick was held all the time

The FC's own roll and pitch angle varied only ~1° in every 40 s block, yet holding that took
continuous stick:

| Time | Pitch stick | Roll stick | FC pitch angle | FC roll angle |
| --- | ---: | ---: | ---: | ---: |
| 4–12 s | +35 (7 %) | +32 | +1.6° | +1.5° |
| 52–60 s | +87 (17 %) | +36 | +4.6° | +1.7° |
| 140–148 s | +104 (21 %) | +66 | +5.7° | +3.2° |
| 196–204 s | +118 (24 %) | +48 | +6.9° | +2.3° |

On the ground the FC read level (pitch +0.1°), but by the end of the pack it reported +5 to +7°
nose-down while a quarter of forward stick was held. Most likely the FC's idea of level drifts
nose-up in flight and was being corrected without noticing.

This is the third attitude-reference issue in two days, after the
[bad Crafty calibration](/log/2026-09-13-crafty-snappy-horizon/) and the
[Air65's 6.5° offset](/log/2026-09-13-air65-takeoff-pitch-up/). It differs by starting at zero and
growing in the air. Vibration can bias an accelerometer and motor 4's imbalance is the obvious
candidate, so fix that first. Then hover a pack with light hands and read the attitude in the
Configurator right after landing.

## What the active pack says about the flying

- **Very gentle attitude:** under 11° of tilt 90 % of the time, under 15° 99 %. Throttle stayed
  between 1367 and 1465.
- **Flat turns:** yaw stick was in use 60–85 % of the time, but real bank in turns was 3.4°
  against 3.1° flying straight. The nose rotates while the craft keeps sliding.
- **Both crashes were turns into something,** not loss of control. In `btfl_004` the craft was
  flying forward while yawing at 60–100 °/s, only 6° nose-down 50 ms before a 7.5 g impact.
- **Throttle does not follow tilt** (correlation about zero in pack 2, +0.5 in the hover pack).

To practise: bank into turns with roll rather than steering on yaw alone, and leave more room
before a turn.

## Other observations

- **Centre of gravity:** front motors 7 % faster than rear, pitch I-term −17. Slightly nose-heavy.
- **Current sensor:** the 1320 A glitch recurs in `btfl_001`, `002` and `004`.
- **Flash:** 16 MB holds about seven minutes at this logging rate. Erase after every two packs.
