---
title: Craft
description: The airframes documented on this site, and which pages apply to each.
weight: 5
toc: false
---

Three airframes share this site. Most pages apply to all of them, because the switch map, rates
and throttle curve are pilot settings rather than craft settings. Pages that describe a specific
frame, its motors or its tune are marked with that craft only.

- **Crafty** — the original 1S whoop. Retired 17–23 September 2026 after
  [an unresolved overheating fault](/log/2026-09-17-crafty-temperature-warning/); its board was
  replaced rather than repaired. On 25 September the old board was
  [cleaned of flux and ink and stopped its runaway heating](/log/2026-09-25-crafty-old-board-flux-cleaning/),
  and is now a bench board.
  [Build spec](/reference/build-betafpvg473-v2-1s/).
- **Phoenix** — the board that replaced Crafty's, in the same frame. Treated as its own craft
  because it's a different physical unit with its own MCU ID, own faults, and its own name —
  not a continuation of Crafty's history. See
  [the full story](/blog/phoenix-rises-a-boards-hard-week/).
- **Air65** — a BetaFPV Air65 added in September 2026.
  [Build spec](/reference/build-air65/).

All three run the same `BETAFPVG473_V2` board target, so the board name does not distinguish
them. Their MCU IDs and their gyros do.
