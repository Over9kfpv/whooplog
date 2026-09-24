---
title: Flight Log
description: Per-flight findings from decoded blackbox data.
weight: 2
toc: false
---

One entry per flight worth analysing. Each records what the flight was for, what the
decoded log showed, and anything left open.

| Date | Entry | Craft | Duration | Verdict |
| --- | --- | --- | --- | --- |
| 2026-09-24 | [First Hover, After Three Wrong Turns](2026-09-24-crafty-first-hover-after-replacement/) | Phoenix | ~17 arms; final hover 9-17s x3 | Fixed and flying; crashflip and fleet settings brought in line |
| 2026-09-23 | [Replacement Board Restored and Recalibrated](2026-09-23-crafty-board-replaced/) | Phoenix | Bench only | Restored, recalibrated, baselined at 63–65 °C no-fan — ready to fly |
| 2026-09-17 | [Chasing a Temperature Warning to a New Board](2026-09-17-crafty-temperature-warning/) | Crafty | 9 logs, 4 packs + bench | Logs clean, fault never isolated; board replaced |
| 2026-09-16 | [Overheating After a Crash, Not From Flying](2026-09-16-crafty-overheat-after-crash/) | Crafty | 9 logs, 2 packs | Motor 4 stalled at full command on ground re-arms |
| 2026-09-13 | [Hover Pack, Crash Pack and the Odd Sound](2026-09-13-crafty-hover-and-crash-review/) | Crafty | 5 logs, 2 packs | Motor 4 imbalance; level reference drifts in flight |
| 2026-09-13 | [Blackbox Review, Rougher After a Crash](2026-09-13-crafty-blackbox-review/) | Crafty | 39 logs | Vibration tripled after one crash; RPM per command normal |
| 2026-09-13 | [The Nose-Up Takeoff, Traced to the Level Reference](2026-09-13-air65-takeoff-pitch-up/) | Air65 | 31 logs, 8 packs | Pitch reference 6.5° off for six arms; recalibrated |
| 2026-09-13 | [Crafty's Snappier Horizon Copied Over](2026-09-13-air65-snappy-horizon/) | Air65 | Bench only | Six settings applied to profile 0; not yet flown |
| 2026-09-13 | [Snappier Horizon for Track Flying](2026-09-13-crafty-snappy-horizon/) | Crafty | Bench only | Six settings changed; bad accelerometer calibration fixed |
| 2026-09-12 | [Blackbox Review of an 18-Arm Session](2026-09-12-air75-blackbox-review/) | Crafty | 434 s armed | Tune healthy; CG forward; decoder voltage bug |
| 2026-09-12 | [Blackbox Review of 16 Logs](2026-09-12-air65-blackbox-review/) | Air65 | 8 m 38 s armed | Balanced; log rate below motor frequency |
| 2026-09-12 | [Bench Setup and Preference Port](2026-09-12-air65-setup/) | Air65 | Bench only | Factory arming switch corrected |
| 2026-09-11 | [001–021 — First Batch on the New Tune](2026-09-11-new-tune-batch/) | Crafty | ~7 min total | Tune confirmed; packs degrading |
| 2026-09-09 | [007–031 — Twenty-Five Flight Batch](2026-09-09-twenty-five-flight-batch/) | Crafty | ~7 min total | Airframe fine; battery run down repeatedly; flash filled |
| 2026-09-08 | [006 — Post-Rebuild Shakedown](2026-09-08-post-rebuild-shakedown/) | Crafty | 30.6 s | Rebuild sound; two open issues |

{{< callout type="info" >}}
Raw `.bbl` logs are **not committed** to this repository — they are working data, kept
locally. Each entry names its source file, and
[Decoding a Blackbox Log](/docs/decode-and-analyze-blackbox/) documents the exact command
that turns one into the numbers quoted here.
{{< /callout >}}
