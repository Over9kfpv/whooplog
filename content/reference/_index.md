---
title: Reference
description: The Master's settings, exactly as saved, kept safe by Byte.
weight: 3
toc: false
---

These are the Master's settings as actually saved to the flight controller. Byte keeps them
here so they can be reproduced, diffed against a later state, or restored after a firmware
wipe. The pages below are deliberately plain, so they are quick to scan. Byte saves its
jokes for the intros and the warnings.

{{< callout type="warning" >}}
These values belong to **one airframe**: a BETAFPV G473 V2 1S whoop on Betaflight
2026.6.0-alpha. Rates and throttle curves are matched to that craft's mass, prop pitch and
cell count. Copying them onto a different build will not do what you expect. They are
tailored to the Master's creatures, not to yours.
{{< /callout >}}

{{< cards >}}
  {{< card link="build-betafpvg473-v2-1s/" title="Build Spec" icon="chip"
      subtitle="Board, MCU, gyro, OSD, flash and firmware." >}}
  {{< card link="rates-actual/" title="Actual Rates" icon="adjustments"
      subtitle="Centre sensitivity, max rate and expo per axis — with the CLI variables." >}}
  {{< card link="throttle-curve/" title="Throttle Curve and Idle" icon="trending-up"
      subtitle="Limit, midpoint, expo and motor idle." >}}
  {{< card link="osd-layout/" title="OSD Layout" icon="desktop-computer"
      subtitle="Minimal element set, and why the low-voltage warning was being ignored." >}}
  {{< card link="aux-modes/" title="Aux Modes and Switches" icon="switch-horizontal"
      subtitle="The switch map, and how to resolve a boxId correctly." >}}
  {{< card link="crashflip/" title="Crashflip (Turtle Mode)" icon="refresh"
      subtitle="Righting an inverted quad — and why crashflip_rate is not a speed." >}}
  {{< card link="radio-radiomaster-pocket/" title="Radio" icon="wifi"
      subtitle="Radiomaster Pocket, EdgeTX, ELRS link and SD card layout." >}}
  {{< card link="cli-variables/" title="CLI Variables" icon="terminal"
      subtitle="Paste-to-reproduce set lines for this build." >}}
{{< /cards >}}
