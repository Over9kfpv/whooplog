---
title: Phoenix Rises, After a Board's Hard Week
description: A board that quietly overheated, a replacement that flipped on every single arm, and the four unrelated reasons why.
date: 2026-09-24
tags: ["hardware", "postmortem", "accelerometer", "tuning"]
---

Crafty doesn't exist anymore. Not because it crashed hard enough to total the frame, and not
because anyone made a single bad call — the board underneath it failed in a way nobody could
pin down, got replaced, and the replacement then refused to fly for reasons that had nothing to
do with each other and everything to do with the order they were found in. What's flying now is
a different physical board, in the same frame, with a new name: **Phoenix**. This is the week
that got it there.

## How Crafty actually died

It didn't die in a crash. [A motor stalled against a jammed prop after a
crash](/log/2026-09-16-crafty-overheat-after-crash/) on 16 September, and repeated ground arms
against that jam cooked enough heat into the board that an OSD temperature warning started
firing. That much was straightforward, and it was fixed — new prop, motor confirmed healthy,
warning gone.

Then on 17 September [it came back with the fault fully cleared](/log/2026-09-17-crafty-temperature-warning/),
under conditions that ruled out the props, the motors, the VTX, and the battery one at a time.
The board would climb into the 70s on the bench with no airflow, and there was no repeatable
cause to point at. That investigation is also the one that caught its own mistake mid-stream: the
"safe" 39–41°C baseline used for comparison on the two days that mattered had been measured **with
a fan blowing on the board**, which is not a bench you can trust. [The write-up on that error
alone](/blog/a-baseline-taken-with-a-fan-on/) is worth reading, because it's the same lesson that
shows up again later in this story, in a different shape.

With no isolated cause and a genuine safety margin question — an AIO board sharing a die with
its own ESC FETs, alarming at 70°C with no clear source — the decision was to replace the board
rather than keep chasing a fault that three separate investigations hadn't found. A new one was
ordered on 18 September.

## The replacement's quiet start

The new board arrived and went in cleanly. [The setup](/log/2026-09-23-crafty-board-replaced/)
was almost boring by comparison: same firmware already flashed (correct for its `BMI270` gyro, no
reflash needed), the old config restored from a pre-replace backup, blade orientation confirmed
to match the Air65, and a fresh accelerometer calibration — because a calibration offset is a
property of one physical sensor, and it does not transfer across a board swap even when
everything else in a config backup does. A careful no-fan thermal baseline came back healthy,
landing close to the Air65's own control reading rather than anywhere near the old board's 70°C
plateau.

Motors confirmed connected. Config confirmed correct. By every check available on the bench,
it was ready to fly.

It was not ready to fly.

## Every single arm flipped

The first real test — arm, slow throttle, an attempt to lift off — flipped immediately. So did
the second. So did the third, and the fourth, and the fifth, across a config reset, a battery
swap, and a physical motor replacement in between. Each fix looked like it should have worked,
and each time the craft flipped again in almost exactly the same way: calm through a slow
throttle ramp, then one single, violent rotation the instant real thrust arrived. Never a
gradual wobble building into a crash — always a flat line, then a spike.

That consistency turned out to be the important clue, but it took four wrong turns to see it,
because four separate real faults were stacked on top of each other, and clearing them out of
order made every fix look like a failure.

### Fault one: a motor that lied to a hand check

The craft flipped consistently toward one side. A wobble-and-freeplay check on that motor —
battery off, spin the bell by hand — found nothing. It span freely, no grit, no bent shaft. By
every mechanical measure, it was fine.

It wasn't. Bidirectional DShot telemetry, once turned back on (it had been reset off along with
everything else), told a completely different story under actual drive current:

| Motor | RPM at bench throttle |
| --- | ---: |
| 1, 3, 4 | 6 800 – 7 267 |
| **2 (rear-left)** | **233** |

Four percent of normal RPM, on a motor that felt perfect by hand. That's the signature of an
electrical fault rather than a mechanical one — most likely a cold or broken phase-wire joint,
something that only shows up once real current is actually asked to flow through it, which a
gentle hand spin will never do. It was replaced, RPM confirmed balanced across all four
afterward — and the craft flipped again on the very next arm, identically.

### Fault two: a battery that looked half-dead and wasn't

The blackbox logs from those flips showed voltage sitting flat around 3.5V before every attempt,
crashing to 3.0–3.2V with 28–39A spikes at the moment of the flip. On a 1S pack that should be
resting well above 4V, that reads like a battery too weak to supply real thrust — so a fresh pack
went in.

Same flip. Same magnitude, same timing.

The battery was never the problem. This craft has a [documented decoder quirk](/log/2026-09-12-air75-blackbox-review/):
`blackbox_decode` misreads its logged voltage field and reports numbers about 13% low. 3.5V in
the CSV is really close to 4.0V — a perfectly healthy pack the whole time. Two real fixes down,
and the craft still flipped exactly the same way it had at the very start.

### Fault three, in two different wrong directions before it was right

The flip's actual signature — dead calm, then one massive single-axis spike the instant real
throttle arrived, never a slow divergence — doesn't point at a bad motor or a weak pack at all.
It points at the flight controller's own sense of which way is which being wrong. That's what
`align_board_yaw` encodes: how the gyro chip is physically soldered onto the board relative to
the frame it's mounted in. Get it wrong, and every self-level correction, every PID axis, every
"which way did I just rotate" answer is computed against the wrong physical direction.

It had been sitting at its default `0` the whole time, since the full config reset that started
this investigation. A CLI export found online, for what looked like the exact same physical
board, set it to `-135`. Applied, and the accelerometer recalibrated fresh against it — which
came back reading a perfect, level `0.0°`.

That felt like confirmation. It wasn't one.

> Recalibrating **after** setting a board-yaw value will always make the resting reading come out
> level, whether the rotation itself is correct or not — calibration just zeroes out whatever
> bias exists *after* the rotation has already been applied to the raw sensor data. A wrong
> rotation and a right one can both "read level." The only way to tell them apart is what happens
> once the craft actually starts moving, and once it did, the flip came back exactly as before.

The file's `mcu_id` — the one identifier that's genuinely unique per physical chip, unlike a
board-design fact like alignment — was checked against the live board. It didn't match. A
different unit entirely. `align_board_yaw` went back to `0`, recalibrated again, and the craft
flipped once more on the next arm — the fourth time in a row that a real, careful fix had
apparently done nothing.

### The detour: patching the wrong problem until it almost worked

With the board's own alignment now an open question again, a second CLI export was found — same
board, same build, same motor spec (`0802 Freestyle GF 1614`) — and this time loaded wholesale,
`mcu_id` mismatch included, on the theory that a shared build target was closer to ground truth
than starting from nothing. Bench tests of each motor's physical position, done earlier under the
wrong `0°` alignment, had suggested the motor wiring didn't match Betaflight's assumed layout —
so a custom motor-mixer table was built by hand, coefficient by coefficient, straight from
Betaflight's own `mixerQuadX` source, to correct what looked like a genuine wiring mismatch.

It almost worked. Single-motor bench tests, propped off, passed clean. But every real flight
attempt still flipped, and this time the failure had a strange shape: one motor pinned at idle
while two others maxed out, in a diagonal pattern that didn't match the axis the gyro was
actually reporting. That mismatch — the motors responding as if correcting one thing, the sensor
reporting another — was the tell that the custom mixer was patching around a *wrong* alignment
rather than fixing a real wiring fault. Once the second file's `align_board_yaw = -135` went back
in, on the stock mixer, with no hand-built correction at all, it held.

`align_board_yaw` was right the second time for the same reason the `mcu_id` mismatch was the
right caution the first time, and those two facts don't contradict each other: board-yaw
describes the PCB itself, identical across every unit of one board model. `mcu_id` and
accelerometer calibration are per-unit facts that never transfer. A mismatched serial number was
real evidence to distrust a *personal* setting from that file. It was never evidence against a
*design* fact the file also happened to contain.

## What actually flew

Arm switch moved back to the pilot's usual AUX4 (the file's own mapping used a switch this radio
didn't have wired), one more fresh calibration under the now-correct alignment, and a hand-held
test first — held off the floor, throttle up, gently tilted — rather than another aggressive
floor liftoff. Stable. Then a real, short hover, logged clean across three arms: motors balanced
within a few percent of each other, gyro noise floor under 0.12°/s throughout, no excursions in
any of them.

[The full technical write-up](/log/2026-09-24-crafty-first-hover-after-replacement/) has the
tables. What matters here is simpler: four real, unrelated faults — a dead motor, a decoder quirk
that faked a dying battery, a board-alignment mistake made twice in two different directions, and
a mixer patch that solved a problem the correct alignment didn't have — each one alone was enough
to produce the exact same flip, and clearing them one at a time, out of order, is what made every
individual fix look like it had failed.

## A new name for a new board

The board never inherited Crafty's `craft_name` — the file it was finally set up from called it
`AIR75 F`, and that was never going to stick. It's a different physical unit, with its own faults
and its own fixes, in the same frame Crafty flew in. It's called **Phoenix** now, and it's
tracked on this site as its own craft rather than a continuation of Crafty's history — Crafty's
pages describe a board that's retired, and conflating the two would make future log entries
ambiguous about which physical hardware a finding applies to.

Once it was flying, the last piece was making sure Phoenix actually matched the rest of the
fleet: the loaded file had brought back a tune, but not the pilot's own switch layout, throttle
curve, or crashflip settings, all of which the earlier config reset had quietly wiped. Crashflip
was mapped to a switch but set to do nothing (`crashflip_rate = 0`). `vcd_video_system` was left
on `NTSC`, which is correct for the Air65 and wrong for this board. Telemetry was off. All of it
got checked against [the pilot's standard setup](/reference/pilot-preferences/) and brought back
in line — recorded in the same log entry as the rest of the fix, since none of it was a fault
that caused the flipping, just settings a full reset had erased along the way.

Phoenix is on the same VTX channel as the Air65 now too, for one less thing to switch on the
goggles between craft.
