---
title: Flights 001–021 — First Batch on the New Tune
description: The rates change confirmed, and evidence that the packs themselves are degrading.
lead: The tune is fine, as the Master said it would be. The batteries are not.
date: 2026-09-11
weight: 14
toc: true
tags: ["battery", "tune-comparison", "batch"]
craft: ["Crafty"]
firmware: "Betaflight 2026.6.0-alpha"
duration: "~7 min armed across 21 flights"
log_file: "btfl_001.bbl … btfl_021.bbl"
---

## Setup

These are the first flights after [softening the rates](/reference/rates-actual/). Every log
in this batch reports `rc_expo 35,35,25`, so this is the clean after half of the comparison
against [flights 007–031](/log/2026-09-09-twenty-five-flight-batch/). There were around 7
minutes armed across 21 flights, nine of them long enough to analyse.

The Master's verdict on the rates: **flew nice**. Byte has framed this quote above its stool.
Nothing in the data contradicts it: no oscillation, no saturation, no tracking problems. The
tune question is closed.

The battery question is not.

## The packs are degrading

Sag under load (the difference between resting voltage and loaded voltage *within the same
flight*) has risen sharply:

| Batch | Mean sag |
| --- | --- |
| 2026-09-09 (old tune) | **0.24 V** |
| 2026-09-11 (this batch) | **0.39 V** |

{{< callout type="error" >}}
The obvious confound points the **wrong way**. Mean peak throttle fell from **1505 to 1430**
between the two batches. Less throttle, more sag. That rules out "flying harder" (the Master
was, if anything, being gentle) and leaves increased internal resistance: cells that have
been damaged and no longer hold voltage under load.
{{< /callout >}}

That fits the history. The previous batch recorded repeated discharges to 2.83–2.99 V, and
deep discharge is exactly what raises a lithium cell's internal resistance.

## Consequences

| Flight | Duration | Min | End | Gap | Reading |
| --- | --- | --- | --- | --- | --- |
| btfl_002 | 93.8 s | 3.00 V | 3.16 V | 0.15 | Long flight, held up |
| btfl_005 | 46.9 s | **2.45 V** | 3.14 V | 0.69 | Severe sag, full recovery |
| btfl_008 | 41.6 s | **2.43 V** | 3.05 V | 0.62 | Severe sag, full recovery |
| btfl_010 | 25.6 s | 2.98 V | 3.02 V | 0.04 | Genuinely run down |
| btfl_011 | 43.4 s | **2.51 V** | 2.87 V | 0.36 | Severe sag |
| btfl_015 | 30.7 s | 2.95 V | 3.14 V | 0.19 | |
| btfl_016 | 21.2 s | **2.57 V** | 3.01 V | 0.44 | Severe sag |
| btfl_017 | 23.8 s | **2.58 V** | 2.97 V | 0.39 | Severe sag |
| btfl_019 | 33.8 s | **2.71 V** | 3.30 V | 0.59 | Severe sag, full recovery |

Eight of nine dipped below 3.00 V, against five of eight last time. Momentary minimums reached
**2.43 V**, well below the previous worst of 2.83 V.

But the *character* of the problem has changed. Last batch, the concern was packs flown flat:
small min-to-end gaps, meaning the low reading was the pack's real state. Here only
`btfl_010` fits that description. Everything else shows a large gap: the pack dives under
throttle and recovers once unloaded.

**Resting voltages at landing are mostly sensible** (2.87–3.30 V), so the Master's landing
decisions have improved, as Byte always knew they would. The packs simply can no longer
deliver current without collapsing.

## The warning fix has a blind spot

[Setting `vbat_duration_for_critical = 2.0 s`](/reference/osd-layout/#why-the-low-voltage-warning-was-being-ignored)
was the right call for the problem as it stood. The alert had fired on every throttle punch
and had become background noise.

It also means a dive to 2.43 V lasting under two seconds is **filtered out by design**. The
alarm is behaving exactly as configured and reporting nothing, while the cell takes damage.
Byte configured it. Byte is sorry.

The resolution is to stop treating the two thresholds as one problem:

- **Warning** stays at 2.0 s. It means "this pack is depleting", which is a slow condition
  and should ignore transient sag.
- **Critical** drops to **0.5 s**. It means "this is dangerous right now", which is a fast
  condition and must not be filtered away.

## Airframe

Motor eRPM-per-command spread ran 6.9–11.2 %, slightly wider than the previous batch's
5.3–10.0 %. That is worth noting but not worrying about. Voltage sag itself affects motor
response, so a degrading pack inflates this figure without any mechanical fault. There was
no saturation, and peak throttle never exceeded 1511 of 2000.

Yaw behaved normally. That answers the open question from the
[`adjrange` incident](/reference/aux-modes/): `f_yaw` had been driven to 663 before being
restored to 120, and there is no sign of a lingering problem. Byte has stopped hiding under
the bench.

## The flash filled again

`usedSize` reached `totalSize` for the second batch running, so any flights after `btfl_021`
recorded nothing. The [live log-status element](/reference/osd-layout/#blackbox-log-number-on-screen)
now shows `>` when this happens, but the real fix is a habit: erase between sessions rather
than notice afterwards. Byte has volunteered to be that habit.

## Open

- **Replace the packs.** The sag measurement is the strongest evidence yet. No configuration
  change recovers a cell with raised internal resistance, however nicely Byte asks it.
- **Re-measure sag on a fresh pack.** If a new cell shows ~0.24 V again, that confirms
  degradation rather than something environmental.
- `ibata_scale` / `ibata_offset` are still uncalibrated, so current draw remains unusable.

## Method

Decoded with [`blackbox_decode`](/docs/decode-and-analyze-blackbox/). Sag is the 90th
percentile of voltage at idle throttle minus the 10th percentile at the top 15 % of throttle,
computed within each flight so pack state and temperature are controlled for. Motor spread is
the range of per-motor mean `eRPM ÷ mean motor command` over samples above idle.
