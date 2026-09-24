---
name: Dirty Workbench Cyberpunk
colors:
  surface: '#101419'
  surface-dim: '#101419'
  surface-bright: '#36393f'
  surface-container-lowest: '#0a0f13'
  surface-container-low: '#181c21'
  surface-container: '#1c2025'
  surface-container-high: '#262a30'
  surface-container-highest: '#31353b'
  on-surface: '#e0e2ea'
  on-surface-variant: '#e2bfb0'
  inverse-surface: '#e0e2ea'
  inverse-on-surface: '#2d3136'
  outline: '#a98a7d'
  outline-variant: '#5a4136'
  surface-tint: '#ffb693'
  primary: '#ffb693'
  on-primary: '#561f00'
  primary-container: '#ff6b00'
  on-primary-container: '#572000'
  inverse-primary: '#a04100'
  secondary: '#ffffff'
  on-secondary: '#273500'
  secondary-container: '#c0f500'
  on-secondary-container: '#546d00'
  tertiary: '#00dbe9'
  on-tertiary: '#00363a'
  tertiary-container: '#00a9b4'
  on-tertiary-container: '#00373b'
  error: '#ffb4ab'
  on-error: '#690005'
  error-container: '#93000a'
  on-error-container: '#ffdad6'
  primary-fixed: '#ffdbcc'
  primary-fixed-dim: '#ffb693'
  on-primary-fixed: '#351000'
  on-primary-fixed-variant: '#7a3000'
  secondary-fixed: '#c0f500'
  secondary-fixed-dim: '#a8d700'
  on-secondary-fixed: '#161f00'
  on-secondary-fixed-variant: '#3b4d00'
  tertiary-fixed: '#7df4ff'
  tertiary-fixed-dim: '#00dbe9'
  on-tertiary-fixed: '#002022'
  on-tertiary-fixed-variant: '#004f54'
  background: '#101419'
  on-background: '#e0e2ea'
  surface-variant: '#31353b'
typography:
  display-lg:
    fontFamily: Space Grotesk
    fontSize: 40px
    fontWeight: '700'
    lineHeight: 48px
    letterSpacing: -0.02em
  headline-lg:
    fontFamily: Space Grotesk
    fontSize: 32px
    fontWeight: '700'
    lineHeight: 40px
    letterSpacing: -0.01em
  headline-lg-mobile:
    fontFamily: Space Grotesk
    fontSize: 26px
    fontWeight: '700'
    lineHeight: 34px
    letterSpacing: -0.01em
  headline-md:
    fontFamily: Space Grotesk
    fontSize: 24px
    fontWeight: '600'
    lineHeight: 32px
  headline-sm:
    fontFamily: Space Grotesk
    fontSize: 18px
    fontWeight: '600'
    lineHeight: 26px
  body-lg:
    fontFamily: Chivo
    fontSize: 16px
    fontWeight: '400'
    lineHeight: 26px
  body-md:
    fontFamily: Chivo
    fontSize: 14px
    fontWeight: '400'
    lineHeight: 22px
  body-sm:
    fontFamily: Chivo
    fontSize: 12px
    fontWeight: '400'
    lineHeight: 18px
  label-lg:
    fontFamily: JetBrains Mono
    fontSize: 13px
    fontWeight: '600'
    lineHeight: 18px
    letterSpacing: 0.04em
  label-md:
    fontFamily: JetBrains Mono
    fontSize: 11px
    fontWeight: '500'
    lineHeight: 16px
    letterSpacing: 0.06em
  label-sm:
    fontFamily: JetBrains Mono
    fontSize: 10px
    fontWeight: '500'
    lineHeight: 14px
    letterSpacing: 0.08em
spacing:
  gutter: 1rem
  margin: 1.5rem
  space-xs: 0.25rem
  space-sm: 0.5rem
  space-md: 0.75rem
  space-lg: 1.25rem
  space-xl: 2rem
---

## Brand & Style

This design system establishes a high-density, tactile technical environment modeled after an underground FPV drone builder’s workstation. It transforms standard documentation architecture into an immersive, utilitarian command console. The aesthetic fuses industrial cyberpunk, raw hardware telemetry, and tactile workshop grit—evoking flux fumes, bench power supplies, and stripped wire insulation.

The target audience consists of micro-quad pilots, embedded systems hackers, electronics tinkerers, and documentation readers who value dense, low-latency technical data over corporate sterility. The interface conveys calculated chaos: deliberate mechanical calibration overlaid with high-contrast warning cues, edge-lit indicator diodes, and raw diagnostic readouts.

### Design Movements & Tone
- **Industrial Cyberpunk & Tech-Brutalism:** Uncompromising 90-degree cuts, chamfered corner notches, raw status monitors, visible structural coordinates, and exposed assembly schematics.
- **Gritty Workshop Tactility:** Matte oil-slick canvas tones, subtle printed circuit traces, micro-grid millimeter cutting-mat markers, distress tick-marks, and neon thermal hazard accents.
- **Dense Telemetry Layout:** Eliminates frivolous white space in favor of compact, monospaced diagnostic headers, pinned pinout callouts, and multi-layered data arrays that respect Hextra’s 3-column structural backbone.

## Colors

The palette simulates high-voltage lab equipment active in low-light garages. The base is constructed from oily, desaturated grays and oxidized chassis metals, illuminated by sharp signal diodes and soldering-iron thermal glows.

### Palette Roles
- **Base Substrates:**
  - Canvas Deep Root: `#0d0f12` (Cold cutting-mat floor, dark void behind panels).
  - Surface Neutral: `#14181d` (Chassis plate, sidebar navigation backplane, card backgrounds).
  - Elevated Plate / Surface Container: `#1c222b` (Active code blocks, diagnostic callout trays, inspectable module bodies).
  - Border & Trace Stroke: `#2a3340` (Structural perimeter rails, inactive trace lines, sub-grid dividers).
- **Primary — Hazard Solder Amber (`#ff6b00` / `#ff9f1c`):** Primary action items, warning badges, active navigation focus indicators, critical thermal limits, and primary command triggers.
- **Secondary — Acid Flux Diode (`#c8ff00`):** Pass states, armed telemetry, successful compile outputs, battery voltage OK signals, and verified pinout markers.
- **Tertiary — Telemetry Electric Cyan (`#00f0ff`):** Frequency spectra, RF/VTX channels, clock signals, firmware hashes, link tags, and active scroll spy indicators.
- **Text & Readout Tiers:**
  - High-Voltage Readout (Text Primary): `#e6edf3` (Crisp reading foreground, uncompromised contrast).
  - Spec-Sheet Slate (Text Secondary): `#8b9bb0` (Metadata, unselected navigation, component descriptors).
  - Solder-Mask Muted (Text Disabled/Ghost): `#445163` (Grid unit legends, inactive pinouts, line numbers).

## Typography

The typographic hierarchy enforces immediate technical legibility across high-density reference materials.

- **Headlines (Space Grotesk):** Provides mechanical, geometric impact without compromising structural alignment. Heading elements (H1–H3) are preceded by monospaced hexadecimal or section index counters (e.g., `// 0x01_BUILD_CONFIG`) rendered in the label font.
- **Prose & Long-Form Body (Chivo):** Crisp, industrial grotesque that maintains high tracking legibility against dark slate backgrounds during extended technical reading sessions.
- **System Telemetry, Navigation & Code (JetBrains Mono):** Dedicated to technical metadata, pin numbers, tables, code snippets, hardware flags, and file-tree manifests. All labels utilize uppercase styling with deliberate tracking to emulate silk-screened electronics legends.

## Layout & Spacing

The framework adapts Hextra’s 3-column architecture into a modular diagnostic workstation. Layout rhythm is rooted in a tight 4px baseline sub-grid that aligns with PCB edge connectors and workbench ruler guides.

### Desktop Layout Anatomy (1280px+)
- **Left Column (Navigation & System Tree):** Fixed-width 260px shelf. Houses folder hierarchies styled as firmware directories, hardware revisions, and diagnostic logs. Includes millimeter ruler ticks along the right structural rail.
- **Center Canvas (Markdown Core):** Fluid documentation terminal spanning a maximum reading width of 820px. Features exposed margin coordinates, status indicators, and sticky section metadata.
- **Right Column (Specs, Pinouts & On-Page TOC):** Fixed-width 240px telemetry panel. Houses the anchor table of contents, live firmware/board specs, serial link logs, and quick jump flags.

### Responsive Breakpoints
- **Mobile (<768px):** Left and right panels collapse into flyout diagnostic drawers accessed via top status-bar toggles. Center canvas margin tightens to `1rem` with edge-to-edge technical callouts.
- **Tablet (768px - 1199px):** Specs/TOC panel docks beneath main articles; navigation remains accessible as a sliding drawer or collapsable rail.
- **Desktop (1200px+):** Full triple-column fixed-depth workbench layout with dual status gutters.

## Elevation & Depth

Visual hierarchy is constructed through machined physical plate layering, mechanical contrast, and edge-lit diode emission rather than soft ambient blur shadows.

- **Ground Level (Backplane):** `#0d0f12` cutting mat texture rendered with a subtle, low-opacity 16px x 16px micro-grid and millimeter crosshairs at 64px increments.
- **Mid-Tier Structural Plates:** `#14181d` chassis panels bounded by solid 1px borders in `#2a3340`. Seams between columns mimic split-casing and modular lab rack mounts.
- **Interactive & Elevated Blocks:** `#1c222b` surfaces for diagnostic alerts, callouts, and flyout terminals. Elevated elements use crisp 1px borders with selective optical highlighting: high-priority panels feature an active top border accent in `#ff6b00` or `#00f0ff`.
- **Illumination & Diode Effects:** Shadows are stark and localized. High-voltage interactive states (active pills, focused search inputs, armed switches) emit tight, low-spread color flares (`0 0 8px rgba(255, 107, 0, 0.4)` or `0 0 8px rgba(0, 240, 255, 0.4)`).
- **Distressed Accents:** Selective callout containers feature hazard-striped hazard bars (45-degree angled micro-stripes of amber and dark gray) along their upper or lateral structural boundary.

## Shapes

The design system is strictly non-rounded (`roundedness: 0`). Curved radii are eliminated to reinforce the feel of machined industrial enclosures, bare circuit boards, cut ribbon cables, and precision electronics.

- **Corner Treatment:** Absolute 0px sharp rectangular corners throughout all interactive surfaces, buttons, tooltips, tags, and inputs.
- **Chamfers & Notches:** Primary callout panels, primary action buttons, and modal dialogs utilize 4px to 6px 45-degree diagonal corner cuts (chamfers) via CSS `clip-path`, emulating beveled FR4 fiberglass PCB boards and CNC-machined carbon fiber drone top-plates.
- **Dividers & Rails:** Structural borders are 1px solid lines, occasionally interrupted by terminal crosshairs (`+`), solder pad circles, or slotted dashed segments.

## Components

### Buttons & Action Triggers
- **Primary (Armed / Execute):** Sharp-cornered or 4px chamfered `#ff6b00` background with solid `#0d0f12` bold monospaced text. Hover triggers an intense backlight glow (`0 0 12px rgba(255, 107, 0, 0.6)`) and shifts background to `#ff9f1c`.
- **Secondary (Telemetry / Probe):** Solid `#14181d` surface with a 1px `#2a3340` outline and `#00f0ff` text. Hover shifts border color to `#00f0ff` and applies an interior `#00f0ff` micro-glow.
- **Destructive (Purge / Disarm):** Black background with an alternating 45-degree diagonal hazard pattern stripe, bordered in `#ff3b30`.

### Chips, Tags & Status Badges
- **Form Factor:** Compact monospaced labels encased in 1px borders with explicit uppercase styling.
- **Hardware Telemetry Tags:** Prefix-coded labels (e.g., `[FREQ: 5.8GHz]`, `[UART: 1]`, `[STATUS: ARMED]`).
- **Indicator Diodes:** Every status chip features an inline 5px square LED glyph: `#c8ff00` for active/healthy, `#ff6b00` for caution/tuning, `#00f0ff` for data streams, and `#ff3b30` for error/short-circuit.

### Technical Callout Boxes (Admonitions)
- **Note / Pinout:** `#14181d` backplane with a 1px `#2a3340` border and a solid 3px electric cyan (`#00f0ff`) left guide rail. Header prefixed with `// INFO_LOG`.
- **Warning / Hazard:** Dark charcoal substrate with an amber-and-black 45-degree hazard stripe header strip (4px height) and bold `#ff6b00` perimeter accents. Header prefixed with `// CAUTION_VOLTAGE`.
- **Terminal Snippets:** Dark canvas `#0d0f12` containing an integrated top rail displaying fake hardware status (e.g., `BAUD: 115200 | TTYUSB0`). Copy buttons utilize a monospaced `[COPY_HEX]` trigger.

### Form Inputs & Terminal Search
- **Search Console:** Zero-radius input fields framed by a 1px `#2a3340` stroke. Background set to `#0d0f12`. Focused states immediately shift the border to `#ff6b00` accompanied by a blinking block cursor (`_`). Text input rendered exclusively in `JetBrains Mono`.
- **Checkboxes & Radios:** Sharp square indicators. Checked state fills with a solid inner square of `#c8ff00` surrounded by a `#14181d` inset border.

### Navigation & Table of Contents (Hextra Specifics)
- **Left Directory Tree:** Styled as an unadorned Linux device file tree with connected dot-leader lines (`├─`, `└─`). Active page marked with a vibrant `#ff6b00` prompt prefix (`> `) and subtle background tint.
- **Right Specs / On-Page TOC:** Minimalist telemetry monitor with active scroll points underscored by an electric cyan (`#00f0ff`) indicator tick and frequency bar glyphs (`|||`).