---
title: whooplog
layout: hextra-home
toc: false
---

<div class="wb-home">

<section class="wb-hero">
<div class="wb-tags">
<span class="wb-tag">1S FPV</span>
<span class="wb-tag amber">BF 2026.6.0-alpha</span>
<span class="wb-tag cyan">BETAFPVG473_V2</span>
</div>

# whooplog <em>bench notes</em>

A working reference for two 1S tinywhoops, Crafty and Air65. Build spec, rates, blackbox workflow, and what the flight logs actually showed.

<div class="wb-cta">
<a class="wb-btn primary" href="{{< relref "/log" >}}">Open flight log</a>
<a class="wb-btn secondary" href="{{< relref "/reference" >}}">Reference</a>
</div>
</section>

<div class="wb-stats">
<div class="wb-stat"><b>2</b><span>Airframes</span></div>
<div class="wb-stat"><b>1S</b><span>Battery</span></div>
<div class="wb-stat"><b>13</b><span>Log entries</span></div>
<div class="wb-stat"><b>0</b><span>GPS / location data</span></div>
</div>

<div class="wb-section-title">// 0x01_SECTIONS</div>

<div class="wb-grid">
<a class="wb-panel" href="{{< relref "/reference" >}}"><span class="idx">[01] REFERENCE</span><h3>Reference</h3><p>Build spec, saved rates, throttle curve and the CLI variables behind them.</p></a>
<a class="wb-panel" href="{{< relref "/docs" >}}"><span class="idx">[02] PROCESS</span><h3>Process docs</h3><p>Pull blackbox logs off the board, decode them, and recover a stuck serial link.</p></a>
<a class="wb-panel" href="{{< relref "/log" >}}"><span class="idx">[03] TELEMETRY</span><h3>Flight log</h3><p>Per-flight findings: motor balance, noise floor, and open issues.</p></a>
<a class="wb-panel" href="{{< relref "/blog" >}}"><span class="idx">[04] WRITE-UPS</span><h3>Blog</h3><p>Longer write-ups tying a session together.</p></a>
</div>

<div class="wb-section-title">// 0x02_AIRFRAMES</div>

<div class="wb-craft">
<a class="wb-panel" href="{{< relref "/craft/crafty" >}}"><span class="idx">[CRAFT: CRAFTY]</span><h3>Crafty</h3><p>Every page and log filed under this airframe.</p></a>
<a class="wb-panel" href="{{< relref "/craft/air65" >}}"><span class="idx">[CRAFT: AIR65]</span><h3>Air65</h3><p>Every page and log filed under this airframe.</p></a>
</div>

{{< callout type="warning" >}}
Everything here describes specific airframes on a Betaflight **2026.6.0-alpha** build. Values are recorded so they can be reproduced and re-checked, not offered as recommendations for other builds. Both boards report target `BETAFPVG473_V2`, so identify a craft by MCU ID or gyro, not board name.
{{< /callout >}}

</div>
