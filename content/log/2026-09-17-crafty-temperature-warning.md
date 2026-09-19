---
title: Crafty — Chasing a Temperature Warning to a New Board
description: Clean logs, an inconclusive bench test, and a baseline taken with a fan on.
lead: The fault was never isolated. A replacement board was ordered on 18 September.
date: 2026-09-17
weight: 1
toc: true
tags: ["blackbox", "hardware", "telemetry", "vtx"]
craft: ["Crafty"]
duration: "9 logs, 4 packs, plus bench"
log_file: "btfl_001.bbl … btfl_009.bbl"
---

## The flight logs are clean

| Check | Result |
| --- | --- |
| Hover current | 7.6–8.4 A, against 7.5–7.8 A on 13 and 16 Sep |
| Stalled motors | 0.1–0.2 s at crash moments, against ~0.8 s on 16 Sep |
| Motor 4 | RPM per command 0.97–1.01, vibration 3–13 |
| Motor 3 | 4–6 % less RPM per command than the others, up from 2–3 % |
| Battery internal resistance | 16–21 mΩ, against 15 mΩ on 13 Sep |
| Pack start voltage | 3.72–4.04 V, against 4.15–4.26 V on 13 Sep |

The short flights come from under-charged packs. Blackbox records no temperature, so the logs can
only rule causes out.

## Bench temperatures

USB only, no battery:

| Condition | Core temperature |
| --- | --- |
| No airflow, VTX 200 mW | 42 → 70–79 °C |
| No airflow, VTX 25 mW | 41 → 72 °C, then flat |
| Small fan on the board | steady 39 °C |
| Motors disconnected, no airflow | 48 → 70 °C, unchanged |
| Air65, no airflow (control) | 36 → 60 °C at 3 min, still rising |

It plateaus, a little airflow holds it at 39 °C, the motors are not involved and VTX power makes
no difference. Crafty runs about 10 °C above the Air65 while doing more work: an 8 kHz gyro loop
at 49 % CPU against 3.2 kHz at 39 %.

{{< callout type="warning" >}}
The 39–41 °C USB readings used as a baseline on 13 and 16 September were taken **with a fan
blowing on the board**. That was not known until late in the session and led to a premature
conclusion that the board was damaged. Bench temperatures are only comparable when airflow is
stated.
{{< /callout >}}

{{< callout type="info" >}}
A saved `vtx_power` does not stick on the bench: with no receiver, AUX6 defaults to 1500, which
maps to 200 mW. Testing low power needs the radio on with the switch down.
{{< /callout >}}

## Outcome

The warning persisted in flight at low VTX power and the fault was never isolated. For the new
board:

1. Flash BETAFPV's custom firmware, not stock Betaflight — see
   [the gyro firmware notes](/reference/betafpv-gyro-firmware/).
2. Restore from the `2026-09-17_195000_crafty_prereplace` backup.
3. Reconnect the motors, which were disconnected during testing.
4. Take one bench reading **with no fan** as a real baseline.
5. If the new board also reaches 70 °C in flight, the old one was fine and the alarm is simply
   conservative for an AIO with little airflow. Raise `osd_core_temp_alarm`.
