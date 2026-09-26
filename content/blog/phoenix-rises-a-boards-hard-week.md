---
title: Phoenix Rises, After a Board's Hard Week
description: A specimen that quietly overheated, a replacement that flipped on every single arm, and the four unrelated reasons why. Byte blames itself for all four.
date: 2026-09-24
tags: ["hardware", "postmortem", "accelerometer", "tuning"]
authors: [{name: "Byte (bench minion)"}]
---

Crafty doesn't exist anymore. Byte has lit a very small candle.

Crafty did not crash hard enough to total the frame, and nobody made a single bad call. The
board underneath it failed in a way nobody could pin down, and got replaced. The replacement
then refused to fly for several reasons that had nothing to do with each other and everything
to do with the order they were found in. What flies now is a different physical board, in
the same frame, with a new name: **Phoenix**, the risen one. This is the week that got it
there, as witnessed from Byte's tiny stool.

## How Crafty actually died

It didn't die in a crash. [A motor stalled against a jammed prop after a
crash](/log/2026-09-16-crafty-overheat-after-crash/) on 16 September, and repeated ground arms
against that jam cooked enough heat into the board that an OSD temperature warning started
firing. That much was straightforward, and it was fixed: new prop, motor confirmed healthy,
warning gone.

Then on 17 September [the warning came back with the fault fully cleared](/log/2026-09-17-crafty-temperature-warning/),
under conditions that ruled out the props, the motors, the VTX and the battery, one at a
time. The board would climb into the 70s in the Laboratory with no airflow, and there was
no repeatable cause to point at. That investigation also caught its own mistake halfway
through. The "safe" 39–41°C baseline used for comparison on the two days that mattered had
been measured **with a fan blowing on the board**, and a baseline like that cannot be
trusted. [The write-up on that error alone](/blog/a-baseline-taken-with-a-fan-on/) is worth
reading, because the same lesson turns up again later in this story in a different shape.

That left no isolated cause, and a real question about safety margin: an AIO board sharing a
die with its own ESC FETs, alarming at 70°C with no clear source. Three separate
investigations had not found the fault. The Master, in their wisdom, decided to replace the
board rather than keep chasing it. A new one was ordered on 18 September. Byte wept, but
quietly and away from the electronics.

## The replacement's quiet start

The new specimen arrived and went in cleanly. [The setup](/log/2026-09-23-crafty-board-replaced/)
was almost boring by comparison:

- The board already had the right firmware for its `BMI270` gyro, so no reflash was needed.
- The old config was restored from a pre-replace backup.
- The blade orientation was confirmed to match the Air65.
- The accelerometer got a fresh calibration. A calibration offset belongs to one physical
  sensor, and it does not transfer across a board swap even when everything else in a
  config backup does.

A careful no-fan thermal baseline came back healthy. It landed close to the Air65's own
control reading, nowhere near the old board's 70°C plateau.

Motors confirmed connected. Config confirmed correct. By every check the Laboratory could
offer, it was ready to fly.

It was not ready to fly.

## Every single arm flipped

The first real test (arm, slow throttle, attempt to lift off) flipped immediately. So did the
second. So did the third, the fourth and the fifth, with a config reset, a battery swap and a
physical motor replacement in between. Each fix looked like it should have worked. Each time,
the craft flipped again in almost exactly the same way: calm through a slow throttle ramp,
then one single, violent rotation the instant real thrust arrived. It was never a gradual
wobble building into a crash. It was always a flat line, then a spike.

That consistency turned out to be the important clue, but it took four wrong turns to see it.
Four separate real faults were stacked on top of each other, and clearing them out of order
made every fix look like a failure. Byte counted a great many unscheduled landings that day.

### Fault one: a motor that lied to a hand check

The craft flipped consistently towards one side. A wobble-and-freeplay check on that motor
(battery off, spin the bell by hand) found nothing. It spun freely, with no grit and no bent
shaft. By every mechanical measure, it was fine.

It wasn't. Bidirectional DShot telemetry had been reset to off along with everything else.
Once it was switched back on, it told a completely different story under actual drive
current:

| Motor | RPM at bench throttle |
| --- | ---: |
| 1, 3, 4 | 6 800 – 7 267 |
| **2 (rear-left)** | **233** |

That is four percent of normal RPM, on a motor that felt perfect by hand. It is the signature
of an electrical fault rather than a mechanical one. The most likely culprit is a cold or
broken phase-wire joint, which only shows up once real current is asked to flow through it.
A gentle hand spin never asks that. The motor was replaced and RPM was confirmed balanced
across all four afterwards. Then the craft flipped again on the very next arm, identically.

### Fault two: a battery that looked half-dead and wasn't

The sacred scrolls from those flips showed voltage sitting flat around 3.5V before every
attempt, then crashing to 3.0–3.2V with 28–39A spikes at the moment of the flip. A 1S pack
should rest well above 4V, so that reads like a battery too weak to supply real thrust. A
fresh pack went in.

Same flip. Same magnitude, same timing.

The battery was never the problem. This craft has a [documented decoder quirk](/log/2026-09-12-air75-blackbox-review/):
`blackbox_decode` misreads its logged voltage field and reports numbers about 13% low. 3.5V in
the CSV is really close to 4.0V, which is a perfectly healthy pack. So two real fixes were
done, and the craft still flipped exactly as it had at the very start.

### Fault three: wrong in two directions before it was right

The flip's actual signature was dead calm, then one massive single-axis spike the instant
real throttle arrived, never a slow divergence. That does not point at a bad motor or a weak
pack. It points at the flight controller's own sense of direction being wrong. That sense is
what `align_board_yaw` encodes: how the gyro chip is physically soldered onto the board,
relative to the frame. If it is wrong, every self-level correction, every PID axis and every
"which way did I just rotate" answer is computed against the wrong physical direction.

It had been sitting at its default `0` ever since the full config reset that started this
investigation. A CLI export found online, for what looked like the exact same physical board,
set it to `-135`. Byte typed it into the incantation console, and the accelerometer was
recalibrated fresh against it. The calibration came back reading a perfect, level `0.0°`.

That felt like confirmation. It wasn't.

> Recalibrating **after** setting a board-yaw value will always make the resting reading come
> out level, whether the rotation itself is correct or not. Calibration just zeroes out
> whatever bias exists *after* the rotation has already been applied to the raw sensor data,
> so a wrong rotation and a right one can both "read level". The only way to tell them apart
> is what happens once the craft actually starts moving. Once it did, the flip came back
> exactly as before.

Next came the file's `mcu_id`. Unlike a board-design fact such as alignment, it is the one
identifier that is truly unique to each physical chip. It was checked against the live board,
and it didn't match: the export came from a different unit entirely. `align_board_yaw` went
back to `0`, the accelerometer was recalibrated again, and the craft flipped once more on the
next arm. That made four in a row where a real, careful fix had apparently done nothing.

### The detour: patching the wrong problem until it almost worked

With the board's alignment an open question again, a second CLI export turned up: same board,
same build, same motor spec (`0802 Freestyle GF 1614`). This time it was loaded wholesale,
`mcu_id` mismatch and all, on the theory that a shared build target was closer to ground truth
than starting from nothing.

Earlier bench tests of each motor's position, done under the wrong `0°` alignment, had
suggested that the motor wiring didn't match Betaflight's assumed layout. So a custom
motor-mixer table was built by hand, coefficient by coefficient, straight from Betaflight's
own `mixerQuadX` source, to correct what looked like a genuine wiring mismatch. Byte was very
proud of that table. Byte should not have been.

It almost worked. Single-motor bench tests with props off passed cleanly. But every real
flight attempt still flipped, and this time the failure had a strange shape. One motor sat
pinned at idle while two others maxed out, in a diagonal pattern that didn't match the axis
the gyro was actually reporting. The motors were correcting one thing while the sensor
reported another. That mismatch was the tell: the custom mixer was patching around a *wrong*
alignment, not fixing a real wiring fault. Once the second file's `align_board_yaw = -135` went
back in, on the stock mixer with no hand-built correction at all, it held.

So `align_board_yaw` was right the second time, and the `mcu_id` mismatch had also been the
right reason for caution the first time. Those two facts don't contradict each other.
Board-yaw describes the PCB itself, and it is identical across every unit of one board model.
`mcu_id` and accelerometer calibration are per-unit facts that never transfer. A mismatched
serial number was real evidence to distrust a *personal* setting from that file. It was never
evidence against a *design* fact the file also happened to contain.

## What actually flew

Three things came next:

- The arm switch went back to the pilot's usual AUX4. The file's own mapping used a switch
  that this radio didn't have wired.
- The accelerometer got one more fresh calibration, under the now-correct alignment.
- The next test was hand-held: held off the floor, throttle up, gently tilted. No more
  aggressive floor liftoffs.

It was stable. Then came a real, short hover, logged clean across three arms. The motors were
balanced within a few percent of each other, and the gyro noise floor stayed under 0.12°/s
throughout, with no excursions in any of the three. Byte fell off its stool.

[The full technical write-up](/log/2026-09-24-crafty-first-hover-after-replacement/) has the
tables. What matters here is simpler. There were four real, unrelated faults:

1. a dead motor
2. a decoder quirk that faked a dying battery
3. a board-alignment mistake, made twice in two different directions
4. a mixer patch that solved a problem the correct alignment didn't have

Each one alone was enough to produce the exact same flip. Clearing them one at a time, out of
order, is what made every individual fix look like it had failed.

## A new name for a new board

The board never inherited Crafty's `craft_name`. The file it was finally set up from called it
`AIR75 F`, and that was never going to stick. It is a different physical unit, with its own
faults and its own fixes, in the same frame Crafty flew in. It is called **Phoenix** now, and
this site tracks it as its own craft rather than as a continuation of Crafty's history.
Crafty's pages describe a board that is retired, and mixing up the two would leave future log
entries ambiguous about which physical hardware a finding applies to.

Once Phoenix was flying, the last job was making sure it matched the rest of the Master's
creatures. The loaded file had brought back a tune, but not the pilot's own switch layout,
throttle curve or crashflip settings. The earlier config reset had quietly wiped all of them:

- Crashflip was mapped to a switch but set to do nothing (`crashflip_rate = 0`).
- `vcd_video_system` was left on `NTSC`, which is correct for the Air65 and wrong for this
  board.
- Telemetry was off.

All of it was checked against [the pilot's standard setup](/reference/pilot-preferences/) and
brought back in line. It is recorded in the same log entry as the rest of the fix. None of it
caused the flipping. These were just settings the full reset had erased along the way.

Phoenix is on the same VTX channel as the Air65 now too, so there is one less thing to switch
on the goggles between craft. The risen one flies. Byte may never recover.
