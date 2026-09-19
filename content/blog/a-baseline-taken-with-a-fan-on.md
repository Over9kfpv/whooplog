---
title: A Baseline Taken With a Fan On
description: How an unstated variable turned a normal board into a damaged one.
date: 2026-09-17
tags: ["hardware", "postmortem", "instrumentation"]
---

The OSD temperature warning kept firing on Crafty. The bench readings from 13 and 16 September
said the board sat at 39–41 °C on USB, so a bench reading of 72 °C on 17 September looked like
damage.

It was not a comparison of like with like. The earlier readings had been taken **with a small
fan blowing on the board**. With no airflow the same board climbs from 41 °C to 72 °C and then
goes flat. The [Air65](/log/2026-09-17-crafty-temperature-warning/), used as a control with no
airflow, reached 60 °C and was still rising at three minutes.

## The shape of the mistake

The baseline was a measurement, and the measurement had an input nobody wrote down. Once
"39 °C" was a number in a table it stopped being "39 °C with a fan", and the fan was only
remembered when the two readings refused to agree.

Nothing in the flight logs could have caught it. Blackbox records no temperature, so the logs
could only rule causes out, and they ruled out everything: hover current, stalled motors, motor
balance and pack resistance were all normal.

## What changed in practice

- Every bench temperature now states its airflow next to the number.
- The replacement board gets one no-fan reading before it flies, so there is a baseline that
  means something.
- If the new board also reaches 70 °C in flight, the old one was probably fine and
  `osd_core_temp_alarm` is the thing to raise.

The board is being replaced regardless. The
[earlier finding](/log/2026-09-16-crafty-overheat-after-crash/) still stands: a motor stalled
against a jammed prop after a crash does produce this heat, in seconds.
