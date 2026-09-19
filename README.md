# whooplog

A working reference for two 1S FPV tinywhoops (Crafty and Air65) — build spec, saved rates, blackbox extraction
and analysis workflow, and per-flight findings.

**Live at <https://over9kfpv.github.io/whooplog/>**

Built with [Hugo](https://gohugo.io/) and the [Hextra](https://github.com/imfing/hextra)
theme, deployed to GitHub Pages by GitHub Actions.

## Local development

```bash
git clone --recurse-submodules git@github.com:Over9kfpv/whooplog.git
cd whooplog
hugo server -D
```

Then open <http://localhost:1313/whooplog/> — note the subpath. `baseURL` carries the
production path, and `hugo server` preserves it, so local development exercises the same
URL structure the deployed site uses.

If you cloned without `--recurse-submodules`:

```bash
git submodule update --init --recursive
```

### Scripts

| Command | Purpose |
| --- | --- |
| `npm run dev` | Dev server including drafts |
| `npm run build` | Production build into `public/` |
| `npm run lint` | markdownlint over `content/` |
| `npm run format` | Prettier |
| `npm run check:paths` | Fail-fast check for root-absolute URLs that break the subpath |

## Sections

- **`/reference`** — hardware spec, rates, throttle curve, CLI variables as saved.
- **`/docs`** — repeatable procedures: pulling blackbox logs, decoding them, recovering a
  stuck serial link, driving the board from Claude Code.
- **`/log`** — per-flight findings from decoded blackbox data.
- **`/blog`** — longer write-ups.

## Scope

Everything here describes two airframes, each a BETAFPV G473 V2 1S whoop running a Betaflight
2026.6.0-alpha build. Every page names its craft in frontmatter. Settings are recorded for reproducibility, not offered as
recommendations for other builds.

Raw `.bbl` blackbox logs are not committed — see `CLAUDE.md` for that policy.

## Relationship to the bench

Content is written from the private `whoopshop` workspace, which talks to the flight
controllers and holds raw logs. This repo publishes the analysis. See the *Bench and blog*
section of `CLAUDE.md` and `.claude/skills/port-bench-entry/`.

## Sources

This site documents the use of several third-party projects. Credit and licence terms belong
to their authors:

- [Betaflight](https://github.com/betaflight/betaflight) — flight controller firmware, GPL-3.0
- [blackbox-tools](https://github.com/betaflight/blackbox-tools) — `blackbox_decode`, GPL-3.0
- [blackbox-log-viewer](https://github.com/betaflight/blackbox-log-viewer) — GPL-3.0
- [betaflight-mcp](https://github.com/bvandevliet/betaflight-mcp) — MCP server, AGPL-3.0

## Licence

Prose and documentation: [CC BY 4.0](LICENSE). Configuration snippets are facts about a
device and are free to use without attribution.
