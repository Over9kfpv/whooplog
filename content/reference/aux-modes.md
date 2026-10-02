---
title: Aux Modes and Switches
description: The switch map, and how to read raw aux output correctly.
lead: Resolving a boxId is the step everyone gets wrong. Byte got it wrong first, so you don't have to.
weight: 6
toc: true
craft: ["Crafty", "Air65", "Phoenix"]
---

The Master's switches, slot by slot. Muscle memory is sacred: every creature gets this exact
map.

## Current switch map

| Slot | Mode | Aux channel | Range | Notes |
| --- | --- | --- | --- | --- |
| 0 | ARM | AUX4 | 1800–2100 | |
| 1 | ANGLE | AUX2 | 900–1300 | Low position of a 3-way switch |
| 2 | HORIZON | AUX2 | 1300–1700 | Mid position — high position is acro |
| 3 | BEEPER | AUX1 | 1700–2100 | |
| 4 | *(cleared)* | — | — | Was OSD DISABLE; removed, see below |
| 5 | FLIP OVER AFTER CRASH | AUX5 | 1700–2100 | Turtle mode |
| 6 | BLACKBOX | AUX1 | 900–2100 | Full span — always active, see below |

Plus one **adjustment** range, which is a separate system from modes:

| Slot | Function | Channel | Range | Notes |
| --- | --- | --- | --- | --- |
| 0 | `ADJUSTMENT_OSD_PROFILE` (CLI value **29**) | AUX3 | 900–2100 | 3-way switch selects [OSD profile](/reference/osd-layout/#switching-profiles-from-a-switch) 1/2/3 |

AUX3 previously carried `OSD DISABLE` on its middle position. That was cleared with
`aux 4 0 0 900 900 0 0` — a zero-width range is how Betaflight represents an empty slot —
because it would have blanked the screen at the same time as selecting profile 2.

AUX2 carries a single 3-position switch: angle at the bottom, horizon in the middle, acro at
the top (acro needs no mode — it is what you get when neither is active).

## VTX power knob

VTX power is not a mode. It is the `vtx` table, which maps ranges of one aux channel to a
band, channel and power level. The Master's knob is **AUX6**, four steps, band and channel left
alone (`0 0`):

| Knob (AUX6) | VTX power | CLI |
| --- | --- | --- |
| 900–1200 | 25 mW | `vtx 0 5 0 0 1 900 1200` |
| 1200–1500 | 100 mW | `vtx 1 5 0 0 2 1200 1500` |
| 1500–1800 | 200 mW | `vtx 2 5 0 0 3 1500 1800` |
| 1800–2100 | 400 mW | `vtx 3 5 0 0 4 1800 2100` |

Fields are `vtx <slot> <aux channel> <band> <channel> <power> <start> <end>`, with the aux
channel zero-indexed like `aux`. The ranges are lost on a restore that drops them, and then the
VTX sits at `vtx_power = 1` (25 mW) and the knob does nothing: weak video, no error. Restored
on Phoenix on 2 October.

{{< callout type="warning" >}}
Never turn the knob up without an antenna on the VTX.
{{< /callout >}}

## Reading raw `aux` output

The CLI prints rows of bare integers:

```text
aux 0 0 3 1800 2100 0 0
aux 5 35 4 1700 2100 0 0
```

The fields are `aux <slot> <boxId> <aux channel> <start> <end> <logic> <linked>`. The aux
channel is zero-indexed, so `0` is AUX1 (RC channel 5).

{{< callout type="error" >}}
**The `boxId` is a `permanentId`, not a position in the `boxId_e` enum.** Resolving it by
counting entries in `src/main/fc/rc_modes.h` gives wrong answers.

The real mapping is the hand-maintained `boxes[]` table in `src/main/msp/msp_box.c`, where
each entry is `{ .boxId = BOXxxx, .boxName = "...", .permanentId = N }`. That table is
**sparse and non-sequential** — it has gaps for removed modes and does not follow enum
declaration order.
{{< /callout >}}

Worked example: `aux 5 35 4 1700 2100` has boxId `35`. Counting through the enum suggests
something entirely unrelated. Grepping the actual table gives the answer:

```bash
grep -oE '\{ *\.boxId *= *BOX[A-Z0-9_]+, *\.boxName *= *"[^"]*", *\.permanentId *= *35 *\}' \
  src/main/msp/msp_box.c
```

```text
{ .boxId = BOXCRASHFLIP, .boxName = "FLIP OVER AFTER CRASH", .permanentId = 35 }
```

Match on `permanentId`, always, against the firmware version actually running — forks and
releases can add modes.

{{< callout type="error" >}}
**`adjrange` has the same trap, one layer deeper.** Its function field is *not* the
`adjustmentFunction_e` enum value, and it is not a box ID either. It indexes
`defaultAdjustmentConfigs[]` in `rc_adjustments.c`, **offset by one**:

```c
#define ADJUSTMENT_FUNCTION_CONFIG_INDEX_OFFSET 1
defaultAdjustmentConfigs[adjustmentRange->adjustmentConfig - ADJUSTMENT_FUNCTION_CONFIG_INDEX_OFFSET]
```

So `ADJUSTMENT_OSD_PROFILE`, enum value 28, is CLI value **29**. Writing the enum value
straight in binds the switch to `ADJUSTMENT_YAW_F` — a *step* adjustment that increments
`f_yaw` continuously while the switch is in range. See
[the OSD page](/reference/osd-layout/#switching-profiles-from-a-switch) for the derivation
script and the incident this caused.
{{< /callout >}}

### Modes versus adjustments

Two different systems, easy to conflate:

- **Modes** (`aux`) turn a boolean on or off — armed, angle, beeper. Identified by
  `permanentId` from `msp_box.c`.
- **Adjustments** (`adjrange`) change a *value* — a PID gain, a profile index. Identified by
  position in `defaultAdjustmentConfigs[]` **plus one**, not by the enum.
  `ADJUSTMENT_MODE_SELECT` functions map switch positions onto discrete values;
  `ADJUSTMENT_MODE_STEP` ones increment continuously, which is why a mistargeted step
  function quietly rewrites a tuning value rather than just doing nothing visible.

If something you want isn't in the mode list, check the adjustment list before concluding it
can't be done from a switch. OSD profile switching exists only as an adjustment.

## Why `small_angle = 180` is correct here

`small_angle` is a pre-arm tilt lock: it refuses arming when the craft is tilted beyond the
given angle. Setting it to 180 disables that check.

That looks alarming in a config diff, and on most builds it would be. Here it is **required**:
turtle mode is mapped to AUX5, and righting an inverted quad means arming while upside down.
A tilt lock would make the feature impossible.

It is unrelated to `angle_limit`, which is the maximum tilt in angle mode. The names are
similar and the settings have nothing to do with each other.

See [Crashflip](/reference/crashflip/) for the mode's own settings — in particular
`crashflip_rate`, which is an auto-stop rather than a speed and ships disabled.

## The BLACKBOX slot is load-bearing

Slot 6 exists so the [live blackbox log number](/reference/osd-layout/#blackbox-log-number-on-screen)
renders on the OSD. Its full-span 900–2100 range is deliberate: assigning `BLACKBOX` to any
range at all makes logging switch-gated, and a full-span range keeps the mode unconditionally
active so behaviour matches an unassigned setup.

Narrowing or removing that line silently changes when logging happens. If blackbox data ever
goes missing, check here first.

## On the Air65

The [Air65](/reference/build-air65/) runs this switch map slot for slot, including the
cleared slot 4. It shipped with **ARM and BEEPER on each other's switches**, which is the
first thing to check on any new craft — see the
[setup log](/log/2026-09-12-air65-setup/).
