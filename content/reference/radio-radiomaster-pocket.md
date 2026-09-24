---
title: Radio — Radiomaster Pocket
description: Handset, firmware, link, and what's on the SD card.
lead: The transmitter side of the setup.
weight: 7
toc: true
craft: ["Crafty", "Air65"]
---

## Handset

| Item | Value |
| --- | --- |
| Model | Radiomaster Pocket |
| Firmware | EdgeTX |
| Screen | 128×64 monochrome |
| Link | ExpressLRS (CRSF) |
| USB serial | `0483:5740`, serial `00000000001B` |
| USB mass storage | `0483:5720` |
| SD card | ~120 MB FAT |

{{< callout type="warning" >}}
The handset and the flight controller share vendor and product IDs — both are STM32 devices
appearing as `0483:5740`. **Distinguish them by USB serial number**, not by VID:PID. The FC is
`3057397C3235`; the radio is `00000000001B`. Mistaking one for the other is easy and leads to
confident, wrong conclusions.
{{< /callout >}}

Screen resolution matters for the Lua scripts: `SCRIPTS/BF/radios.lua` looks up a layout by
resolution string and hard-fails with `<resolution> not supported` if there is no entry.
`128x64` is supported, so the Pocket works. An unsupported handset throws that assertion
immediately rather than misbehaving subtly.

## USB modes

EdgeTX exposes three, switched at **SYS → HARDWARE → USB Mode**:

| Mode | USB ID | Use |
| --- | --- | --- |
| Serial | `0483:5740` | EdgeTX CLI |
| Storage (SD) | `0483:5720` | Copying files to the card |
| Joystick | — | Simulators |

Set to `Ask` and the radio prompts on each connect.

## The EdgeTX CLI

Serial mode gives a `>` prompt at 115200 baud. Available commands:

```text
beep [<frequency>] [<duration>]
ls <directory>
read <filename>
readsd <start sector> <sectors count> <read buffer size (sectors)>
testsd
play <filename>
reboot [wdt]
set <what> <value>
serialpassthrough <port type> <port number>
help [<command>]
```

Note it is **read-only for the filesystem** — `ls` and `read` exist, but there is no write or
copy. Putting files on the card requires Storage mode.

`serialpassthrough` is the route for flashing ExpressLRS hardware through the handset.

{{< callout type="info" >}}
Unlike the flight controller, the EdgeTX CLI needs no special entry sequence — just connect
and press Enter for a prompt. This is the opposite of Betaflight, which requires
[a bare `#` with no line ending](/docs/serial-recovery/).
{{< /callout >}}

## SD card contents

Top level: `FIRMWARE`, `LOGS`, `MODELS`, `RADIO`, `SCREENSHOTS`, `SCRIPTS`, `SOUNDS`,
`BACKUP`, `edgetx.sdcard.version`.

`/SCRIPTS/TOOLS/` holds the tools that appear in the radio's TOOLS menu:

- `elrsV3.lua` — ExpressLRS configuration
- `TBSAgentLite.lua` — TBS device config
- `WizardLoader.lua` — model setup wizard
- `bf.lua`, `bfCms.lua` — Betaflight, see [TX Lua scripts](/docs/tx-lua-scripts/)
- assorted games

A tool's menu name comes from a marker in the script itself, not the filename:

```lua
local toolName = "TNS|Betaflight setup|TNE"
```

## Sounds

`SOUNDS/en/` is the voice pack for the selected language. Flight-mode and telemetry callouts
sit in that directory under names of **six characters or fewer** (`armed.wav`, `lowbat.wav`,
`disarm.wav`). `SOUNDS/en/SYSTEM/` holds the fixed-name clips EdgeTX plays for its own
events, plus the numbers and units it stitches together when speaking a telemetry value.

Every clip is **16-bit signed PCM, mono, 16 kHz**. Stock alerts run roughly 0.6 to 1.5 s.

These are the system alerts worth knowing, because their filenames are fixed by EdgeTX and
are not obvious from the radio's menus:

| File | Plays when |
| --- | --- |
| `SYSTEM/telemko.wav` | telemetry lost |
| `SYSTEM/telemok.wav` | telemetry recovered |
| `SYSTEM/thralert.wav` | throttle not at idle at power-on |
| `SYSTEM/swalert.wav` | a switch is out of position at power-on |
| `SYSTEM/hello.wav` | radio boot |
| `SYSTEM/rxko.wav` | receiver signal lost |

### Custom alerts

Those four alert clips are replaced with Super Mario Bros. effects — pipe for telemetry lost,
power-up for telemetry recovered, small jump for the throttle warning, super jump for the
switch warning. A distinct, recognisable sound per event beats four variations on the same
synthesised voice, particularly for telemetry loss, which needs to register while flying.

Masters live in the workbench at `radio-scripts/SOUNDS/`, which mirrors the card's layout, and
are copied over in Storage mode. `tools/mk_edgetx_sound.sh` does the conversion: it resamples
to 16 kHz mono 16-bit, trims leading and trailing silence, normalises to −3 dBFS and warns
above 2 s.

{{< callout type="warning" >}}
Keep alert clips under about a second. A warning that outlasts the event it describes stops
being information, and the startup warnings repeat until you clear them.
{{< /callout >}}

## Link

ExpressLRS over CRSF, with telemetry enabled on the FC side (`feature TELEMETRY`). The CRSF
link is what carries MSP to the Betaflight Lua scripts, which is why those work even when
[MSP over USB does not](/docs/betaflight-mcp-claude-code/).
