---
title: Crafty's Old Board — Cleaning Flux and Ink Stopped the Runaway Heating
description: The board pulled on 17 September overheated past 85 °C with no fan; after two IPA cleanings it levels off at 72 °C.
lead: Usable as a bench board, not yet flown. The residue was baked flux with marker ink dissolved in it.
date: 2026-09-25
weight: 1
toc: true
tags: ["hardware", "telemetry", "vtx", "safety"]
craft: ["Crafty"]
duration: "Bench only — no flight"
log_file: ""
---

The board pulled from Crafty after the [unresolved temperature warning](/log/2026-09-17-crafty-temperature-warning/)
went back on the bench to see whether it could still be used. With no airflow it overheated past
85 °C and was still climbing. After two isopropyl cleanings it levels off at 72 °C. It has not
been flown, and nothing was tested under battery or motor load.

{{< callout type="warning" >}}
**The VTX must not run without an antenna.** The bench had none fitted. The board is now saved
at `vtx_power = 1` (25 mW), with the AUX6 power mapping cleared and **VTX PIT MODE** (mode 39)
always on (`aux 4 39 0 900 2100 0 0`). That is not "off": Betaflight has no VTX off. Unplug the
board between tests.
{{< /callout >}}

## The board

`BETAFPVG473_V2` on the BETAFPV dev firmware `2026.6.0-alpha`, `craft_name = Crafty`,
`vcd_video_system = AUTO`, `osd_displayport_device = MAX7456`, gyro **ICM42622P** at 8 kHz, 47 %
CPU. The gyro separates it from the [replacement board](/log/2026-09-23-crafty-board-replaced/),
which has a BMI270. The MCU ID was not read.

Photos showed the battery-lead pads (`+` and `–`) coated in black baked flux, with uneven solder
around the R001 shunt. The pink tint is **marker ink** used to colour parts, which dissolved into
the flux. It is not damage.

## Bench temperatures

USB only, no battery, no motors. Core temperature from `status`, sampled every 12–15 s.

<div class="wl-chart"><style>
.wl-chart{--bg:#fcfcfb;--ink:#0b0b0b;--mute:#52514e;--grid:#e6e5e0;--band:#dcdad3;--s1:#2a78d6;--s2:#eb6834;--s3:#1baf7a;position:relative;margin:1.5rem 0;color:var(--ink);background:var(--bg);border:1px solid var(--grid);border-radius:8px;padding:12px 12px 8px}
:is(.dark) .wl-chart{--bg:#1a1a19;--ink:#ffffff;--mute:#c3c2b7;--grid:#34332f;--band:#2c2b28;--s1:#3987e5;--s2:#d95926;--s3:#199e70}
.wl-chart .wl-svg{width:100%;height:auto;display:block;font-family:inherit}
.wl-chart .wl-title{font-weight:600;margin:0 0 2px}
.wl-chart .wl-sub{color:var(--mute);font-size:.85rem;margin:0 0 8px}
.wl-chart .wl-legend{display:flex;flex-wrap:wrap;gap:4px 16px;font-size:.85rem;margin:0 0 6px;color:var(--ink)}
.wl-chart .wl-legend span{display:inline-flex;align-items:center;gap:6px}
.wl-chart .wl-legend i{display:inline-block;width:22px;height:0;border-top:3px solid}
.wl-chart .wl-tip{position:absolute;pointer-events:none;display:none;background:var(--bg);color:var(--ink);border:1px solid var(--grid);border-radius:6px;padding:6px 9px;font-size:.8rem;box-shadow:0 2px 8px rgba(0,0,0,.18);white-space:nowrap;z-index:2}
.wl-chart .wl-cross{stroke:var(--mute);stroke-width:1;display:none}
.wl-chart details{margin-top:6px;font-size:.85rem}
.wl-chart summary{cursor:pointer;color:var(--mute)}
.wl-chart table{width:100%;margin-top:6px}
</style><p class="wl-title">Core temperature, no fan, old Crafty board</p><p class="wl-sub">Cleaning the flux and ink slowed the climb and lowered the plateau. USB only, no battery. Hover for values.</p><div class="wl-legend"><span><i style="border-color:var(--s1)"></i>Not cleaned</span><span><i style="border-color:var(--s2)"></i>After first cleaning</span><span><i style="border-color:var(--s3)"></i>After second cleaning</span><span><i style="border-color:var(--s3);border-top-style:dashed"></i>Second cleaning, repeat run</span></div><svg class="wl-svg" viewBox="0 0 760 400" role="img" aria-label="Core temperature over time on the old Crafty board with no fan: it passes 85 degrees uncleaned, then plateaus at 75 degrees after the first cleaning and 72 degrees after the second, above the 70 degree alarm.">
<rect x="56" y="154.5" width="580" height="10.6" fill="var(--band)"/>
<line x1="56" x2="636" y1="340.0" y2="340.0" stroke="var(--grid)" stroke-width="1"/>
<text x="48" y="344.0" text-anchor="end" fill="var(--mute)" font-size="12">30</text>
<line x1="56" x2="636" y1="287.0" y2="287.0" stroke="var(--grid)" stroke-width="1"/>
<text x="48" y="291.0" text-anchor="end" fill="var(--mute)" font-size="12">40</text>
<line x1="56" x2="636" y1="234.0" y2="234.0" stroke="var(--grid)" stroke-width="1"/>
<text x="48" y="238.0" text-anchor="end" fill="var(--mute)" font-size="12">50</text>
<line x1="56" x2="636" y1="181.0" y2="181.0" stroke="var(--grid)" stroke-width="1"/>
<text x="48" y="185.0" text-anchor="end" fill="var(--mute)" font-size="12">60</text>
<line x1="56" x2="636" y1="128.0" y2="128.0" stroke="var(--grid)" stroke-width="1"/>
<text x="48" y="132.0" text-anchor="end" fill="var(--mute)" font-size="12">70</text>
<line x1="56" x2="636" y1="75.0" y2="75.0" stroke="var(--grid)" stroke-width="1"/>
<text x="48" y="79.0" text-anchor="end" fill="var(--mute)" font-size="12">80</text>
<line x1="56" x2="636" y1="22.0" y2="22.0" stroke="var(--grid)" stroke-width="1"/>
<text x="48" y="26.0" text-anchor="end" fill="var(--mute)" font-size="12">90</text>
<text x="56.0" y="360" text-anchor="middle" fill="var(--mute)" font-size="12">0</text>
<text x="159.6" y="360" text-anchor="middle" fill="var(--mute)" font-size="12">100</text>
<text x="263.1" y="360" text-anchor="middle" fill="var(--mute)" font-size="12">200</text>
<text x="366.7" y="360" text-anchor="middle" fill="var(--mute)" font-size="12">300</text>
<text x="470.3" y="360" text-anchor="middle" fill="var(--mute)" font-size="12">400</text>
<text x="573.9" y="360" text-anchor="middle" fill="var(--mute)" font-size="12">500</text>
<text x="346.0" y="382" text-anchor="middle" fill="var(--mute)" font-size="12">Seconds since first reading (USB power only, no battery, no fan)</text>
<text transform="translate(14 181.0) rotate(-90)" text-anchor="middle" fill="var(--mute)" font-size="12">Core temperature, °C</text>
<line x1="56" x2="636" y1="128.0" y2="128.0" stroke="var(--ink)" stroke-width="1.5" stroke-dasharray="2 4"/>
<text x="644" y="132.0" fill="var(--ink)" font-size="12">OSD alarm 70 °C</text>
<text x="644" y="163.8" fill="var(--mute)" font-size="12">Replacement board</text>
<text x="644" y="177.8" fill="var(--mute)" font-size="12">63–65 °C plateau</text>
<polyline points="56.0,149.2 73.6,112.1 88.1,90.9 91.2,85.6 102.6,75.0 108.8,59.1 117.1,59.1 127.5,53.8 132.6,53.8 145.1,48.5 179.2,48.5" fill="none" stroke="var(--s1)" stroke-width="2.5" stroke-linejoin="round" stroke-linecap="round"/>
<circle cx="179.2" cy="48.5" r="4" fill="var(--s1)" stroke="var(--bg)" stroke-width="2"/>
<polyline points="56.0,302.9 70.5,244.6 85.0,196.9 99.5,159.8 115.0,143.9 129.5,138.6 144.0,128.0 158.5,122.7 174.1,122.7 188.6,117.4 203.1,112.1 217.6,117.4 233.1,112.1 247.6,112.1 262.1,106.8 276.6,112.1 292.1,106.8 306.6,106.8 321.1,106.8 335.6,106.8 351.2,106.8 365.7,106.8 380.2,106.8 394.7,106.8 410.2,101.5 424.7,101.5 439.2,101.5 453.7,101.5 469.2,101.5 483.8,101.5 498.2,106.8 512.8,101.5 528.3,101.5 542.8,101.5 557.3,101.5 571.8,101.5 587.3,101.5 601.8,101.5 616.3,101.5 630.8,101.5" fill="none" stroke="var(--s2)" stroke-width="2.5" stroke-linejoin="round" stroke-linecap="round"/>
<circle cx="630.8" cy="101.5" r="4" fill="var(--s2)" stroke="var(--bg)" stroke-width="2"/>
<polyline points="56.0,260.5 70.5,239.3 85.0,223.4 100.5,212.8 115.0,202.2 129.5,191.6 144.0,181.0 159.6,170.4 174.1,159.8 188.6,154.5 203.1,149.2 218.6,143.9 233.1,138.6 327.4,122.7 341.9,117.4 357.4,117.4 371.9,117.4 386.4,117.4 400.9,117.4 416.4,117.4 430.9,117.4 445.4,117.4 459.9,117.4 475.5,117.4 490.0,117.4 504.5,112.1" fill="none" stroke="var(--s3)" stroke-width="2.5" stroke-linejoin="round" stroke-linecap="round"/>
<circle cx="504.5" cy="112.1" r="4" fill="var(--s3)" stroke="var(--bg)" stroke-width="2"/>
<polyline points="56.0,244.6 70.5,228.7 85.0,218.1 100.5,207.5 115.0,191.6 129.5,181.0 144.0,170.4 159.6,165.1 174.1,149.2 188.6,149.2 203.1,138.6 217.6,138.6 233.1,133.3 247.6,133.3 262.1,128.0 276.6,128.0 292.1,122.7 306.6,122.7 321.1,117.4 335.6,117.4 351.2,117.4 365.7,117.4 380.2,117.4 394.7,117.4 410.2,117.4 424.7,112.1" fill="none" stroke="var(--s3)" stroke-width="2.5" stroke-linejoin="round" stroke-linecap="round" stroke-dasharray="6 4"/>
<circle cx="424.7" cy="112.1" r="4" fill="var(--s3)" stroke="var(--bg)" stroke-width="2"/>
<text x="187.2" y="52.5" text-anchor="start" fill="var(--ink)" font-size="13" font-weight="600">Not cleaned</text>
<text x="187.2" y="67.5" fill="var(--mute)" font-size="12">stopped at 85 °C cutoff</text>
<text x="638.8" y="95.5" text-anchor="start" fill="var(--ink)" font-size="13" font-weight="600">First cleaning</text>
<text x="638.8" y="110.5" fill="var(--mute)" font-size="12">75 °C</text>
<text x="504.5" y="144.1" text-anchor="middle" fill="var(--ink)" font-size="13" font-weight="600">Second cleaning</text>
<text x="504.5" y="158.1" text-anchor="middle" fill="var(--mute)" font-size="12">72 °C, two runs</text>
<line class="wl-cross" y1="22" y2="340"/></svg><div class="wl-tip"></div><details><summary>Show as a table</summary><table><thead><tr><th>Time</th><th>Not cleaned</th><th>First cleaning</th><th>Second cleaning</th><th>Repeat run</th></tr></thead><tbody><tr><td>30 s</td><td>77 °C</td><td>58 °C</td><td>52 °C</td><td>53 °C</td></tr><tr><td>60 s</td><td>83 °C</td><td>67 °C</td><td>56 °C</td><td>58 °C</td></tr><tr><td>120 s</td><td></td><td>71 °C</td><td>64 °C</td><td>66 °C</td></tr><tr><td>180 s</td><td></td><td>73 °C</td><td>68 °C</td><td>69 °C</td></tr><tr><td>240 s</td><td></td><td>74 °C</td><td>70 °C</td><td>71 °C</td></tr><tr><td>300 s</td><td></td><td>74 °C</td><td>72 °C</td><td>72 °C</td></tr><tr><td>360 s</td><td></td><td>75 °C</td><td>72 °C</td><td></td></tr><tr><td>420 s</td><td></td><td>74 °C</td><td>72 °C</td><td></td></tr><tr><td>500 s</td><td></td><td>75 °C</td><td></td><td></td></tr></tbody></table></details><script>(function(){var r=document.currentScript.parentNode,svg=r.querySelector('.wl-svg'),tip=r.querySelector('.wl-tip'),D={"s1": [[0, 66], [17, 73], [31, 77], [34, 78], [45, 80], [51, 83], [59, 83], [69, 84], [74, 84], [86, 85], [119, 85]], "s2": [[0, 37], [14, 48], [28, 57], [42, 64], [57, 67], [71, 68], [85, 70], [99, 71], [114, 71], [128, 72], [142, 73], [156, 72], [171, 73], [185, 73], [199, 74], [213, 73], [228, 74], [242, 74], [256, 74], [270, 74], [285, 74], [299, 74], [313, 74], [327, 74], [342, 75], [356, 75], [370, 75], [384, 75], [399, 75], [413, 75], [427, 74], [441, 75], [456, 75], [470, 75], [484, 75], [498, 75], [513, 75], [527, 75], [541, 75], [555, 75]], "s3": [[0, 45], [14, 49], [28, 52], [43, 54], [57, 56], [71, 58], [85, 60], [100, 62], [114, 64], [128, 65], [142, 66], [157, 67], [171, 68], [262, 71], [276, 72], [291, 72], [305, 72], [319, 72], [333, 72], [348, 72], [362, 72], [376, 72], [390, 72], [405, 72], [419, 72], [433, 73]], "s3b": [[0, 48], [14, 51], [28, 53], [43, 55], [57, 58], [71, 60], [85, 62], [100, 63], [114, 66], [128, 66], [142, 68], [156, 68], [171, 69], [185, 69], [199, 70], [213, 70], [228, 71], [242, 71], [256, 72], [270, 72], [285, 72], [299, 72], [313, 72], [327, 72], [342, 72], [356, 73]]},N={"s1": "Not cleaned", "s2": "After first cleaning", "s3": "After second cleaning", "s3b": "After second cleaning, repeat"},L=56,Rr=636,XM=560,C=r.querySelector('.wl-cross');
function at(d,t){if(t<d[0][0]||t>d[d.length-1][0])return null;for(var i=0;i<d.length-1;i++){if(d[i][0]<=t&&t<=d[i+1][0]){var a=d[i],b=d[i+1];return b[0]==a[0]?a[1]:a[1]+(b[1]-a[1])*(t-a[0])/(b[0]-a[0]);}}return d[d.length-1][1];}
svg.addEventListener('pointermove',function(e){var bb=svg.getBoundingClientRect(),vx=(e.clientX-bb.left)*760/bb.width;if(vx<L||vx>Rr){tip.style.display=C.style.display='none';return;}var t=Math.round((vx-L)/(Rr-L)*XM),h='<b>'+t+' s</b>';for(var k in D){var v=at(D[k],t);if(v!=null)h+='<br>'+N[k]+': '+Math.round(v)+' °C';}tip.innerHTML=h;tip.style.display='block';C.setAttribute('x1',vx);C.setAttribute('x2',vx);C.style.display='block';var rb=r.getBoundingClientRect();tip.style.left=Math.min(e.clientX-rb.left+14,rb.width-tip.offsetWidth-8)+'px';tip.style.top=(e.clientY-rb.top+14)+'px';});
svg.addEventListener('pointerleave',function(){tip.style.display=C.style.display='none';});})();</script></div>

| Condition | Core temperature |
| --- | --- |
| Not cleaned, no fan | 66 → 85 °C in ~2 min, still rising (stopped at the 85 °C cutoff) |
| Not cleaned, fan on, `pid_process_denom` 4 | 45 °C, flat |
| After first cleaning, no fan | 37 → 70 °C in ~85 s, then 74–75 °C for ~7 min |
| After second cleaning, no fan | 45 → 68 °C in 3.4 min, then 72 °C flat (73 °C last read) |
| After second cleaning, repeat from cold, 6 min | 48 → 72 °C at 5 min, then flat (73 °C last read) |
| Replacement board, no fan (23 Sep) | 63–65 °C at 3.5 min |

The 17 September bench reading of this board, a 72 °C plateau, matches the second-cleaning
result. Only the uncleaned run looks like a fault. Each cleaning lowered the plateau and slowed
the climb, which points at the residue, though the starting conditions differed from run to run.

Halving the PID rate (`pid_process_denom` 2 → 4) changed nothing: CPU stayed at 47 %. The 8 kHz
gyro loop cannot be lowered on this board.

## What is and is not known

**Known:** the runaway heating went away after cleaning. The board is stable with the fan on
(45 °C) and plateaus at 72 °C without it. Config, gyro and OSD are intact.

**Not known:**

- Whether flux and ink leakage was the whole cause. It is the likeliest reading, not proven.
- Which cleaning did what: both were done between runs.
- Current draw, motor outputs and DShot telemetry. No battery was connected.
- Flight behaviour. On 17 September the alarm tripped in flight at low VTX power.

72 °C is still above `osd_core_temp_alarm = 70`, so on the bench without airflow the warning
still fires.

## Outcome

Usable as a **bench and programming board**. Before flying it:

1. Fit a VTX antenna, remove the always-on pit mode (`aux 4 0 0 900 900 0 0`) and restore the AUX6 power mapping.
2. Run a props-off battery test for current draw and all four motor outputs.
3. Fly a short pack and read the temperature on the OSD. If it warns, raise
   `osd_core_temp_alarm` (about 85) rather than chasing it. The replacement board's 63–65 °C
   shows the airframe is not the problem.

{{< callout type="info" >}}
IPA cleaning is the cheapest test for a board with heavy flux on it, and it should come before a
replacement is ordered. It changed the result here, though it is not proven to be the whole cause.
{{< /callout >}}
