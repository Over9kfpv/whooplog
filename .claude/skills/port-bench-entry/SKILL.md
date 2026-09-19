---
name: port-bench-entry
description: Port a whoopshop bench entry (flight review, tuning change, hardware finding) into the whooplog site as a /log page, a /blog post, or a /reference update. Use when the user says "update the whooplog", "port the log entries", "publish this to the blog", or asks what the site is missing compared to the bench.
---

# Port a bench entry to the whooplog

**whoopshop** (`/home/hans/Projects/whoopshop`) is the private bench: it talks to the flight
controllers, holds raw logs and config backups, and its `content/log/` is the working journal.
**whooplog** (this repo) is the public Hugo site. Nothing is copied automatically; this skill is
the procedure.

"The whooplog" means this repo, not `whoopshop/content/log/`. If the request is ambiguous,
check which one is meant before writing.

## 1. Find what is missing

```bash
ls /home/hans/Projects/whoopshop/content/log
ls content/log content/blog
git -C /home/hans/Projects/whoopshop log --oneline -10
```

A bench page is missing here if no `content/log/` file has the same date and topic. Also check
`git -C /home/hans/Projects/whoopshop diff` for setting changes recorded only in
`content/reference/` there (rates, switches, crashflip) and mirror them into
`content/reference/`.

Read the bench page in full before porting. Never port a page from its title.

## 2. Choose the destination

| Bench content | Goes to |
| --- | --- |
| Flight/blackbox review, tuning change, bench session | `content/log/YYYY-MM-DD-slug.md` |
| A lesson that outlives the session (a mistake, a method) | `content/blog/slug.md` |
| A changed pilot setting or build fact | edit the matching `content/reference/*.md` |
| A repeatable procedure | `content/docs/` |

One session often yields a log page plus, at most, one blog post.

## 3. Translate the frontmatter

| whoopshop | whooplog |
| --- | --- |
| `craft_name: "AIR65 F"` | `craft: ["Air65"]` |
| `craft_name: "Crafty"` | `craft: ["Crafty"]` |
| `craft_name: "AIR75 F"` | `craft: ["Crafty"]`: same `DeviceUID …303938` board, so same drone |
| `tags: [...]` | `tags: [...]`, same vocabulary (`blackbox`, `tuning`, `hardware`, `telemetry`, `modes`, `vtx`, `safety`, `accelerometer`) |
| `date: …T…Z` | `date: YYYY-MM-DD` |
| `log_file` | keep, as the source filename only |
| (none) | add `description`, `lead`, `weight`, `toc: true`, `firmware`, `duration` |

`weight` orders the sidebar **newest first, lowest weight first**. Adding a page means
renumbering: newest gets the lowest number, and every older log page shifts up by one.

## 4. Rewrite, don't copy

- Reference voice, findings stated as findings. Condense; keep tables, numbers, commands.
- Drop the H1 (the theme renders `title`), the `# Title` restatement, and the "Interactive
  version" artifact links. Drop `sound.mp4`/`motor.jpeg` pointers; those files are not in this repo.
- `> [!WARNING]` becomes `{{< callout type="warning" >}}…{{< /callout >}}`. NOTE/TIP become
  `type="info"`.
- Bench links like `/log/x.html` become `/log/x/`. Never hardcode `/whooplog/`, and never write
  raw HTML with root-absolute paths (see CLAUDE.md).
- Do not link to whoopshop-only pages (`pilot-preferences`, `tagging`, `tools/`). Restate the
  fact or omit it.
- Per-craft settings stay per-craft. Never phrase one craft's setting as a recommendation.

## 5. Update the log index

`content/log/_index.md` holds a **hand-maintained table** of every log page. It does not
generate itself, so a new page that is not added there is invisible on `/log/` even though
it shows in the sidebar. Add a row (newest first) with date, linked title, craft, duration and
a one-line verdict.

## 6. Verify and ship

```bash
hugo --gc --minify --cleanDestinationDir --printPathWarnings
npm run check:paths
```

Then commit only the files you wrote and push `main`; that deploys via GitHub Actions. Leave
unrelated uncommitted files (for example an edited reference page) out of the commit and say so.

## Automatic publishing

The bench (`whoopshop/CLAUDE.md`) instructs Claude to run this procedure by itself whenever a
new `content/log/` entry is written there or a shared pilot setting changes. No extra
confirmation is needed for the push. Never publish raw `.bbl`/`.csv` files, config backups,
or anything containing coordinates or credentials.
