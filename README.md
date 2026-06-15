# Work by PKJ — Painting Gallery

A website showcasing Peter K. Johnson's paintings, published with GitHub Pages.

- **Live site:** https://webexplorations.github.io/workByPKJ/
- **Local copy:** `/Users/peterj1/Documents/workByPKJ/`
- **GitHub repo:** https://github.com/webexplorations/workByPKJ

---

## How it works (the short version)

The paintings live in your Obsidian vault. A build step reads them, resizes the
images for the web, and writes a single data file (`paintings.json`) that the
website loads.

```
Obsidian artwork folders  ──build──▶  paintings.json + resized images  ──git push──▶  GitHub Pages
```

You do **not** edit the website by hand. You edit your paintings in Obsidian,
then ask Claude to rebuild and publish.

---

## To update the site

1. **Make your changes in Obsidian** — add new paintings (via
   `process_paintings.command`), write or edit `## Description` text, change
   tags, or adjust which paintings are featured.
2. **Open this folder in Claude Code** and say something like:
   *"Rebuild the painting site from Obsidian and push it."*
3. Claude will:
   - Re-scan the Obsidian artwork folders
   - Resize any new images to 600px
   - Regenerate `paintings.json`
   - Commit and push to GitHub (the live site updates in ~1 minute)

That's it. If you'd rather do it yourself, the build logic is a Python script
Claude runs — just ask Claude to show it to you.

---

## How paintings get included

A painting appears on the site when its Obsidian note is **tagged `book`**.

```yaml
---
date: 2024-05-31
medium: watercolor
dimensions: 9x12
tags:
  - book          ← required for the painting to appear on the site
  - flowers       ← category tags (shown in the dropdown menu)
related: []
---

![[myPainting.jpeg]]

## Description
The story behind the painting goes here.
```

Key points:

- **`book` tag** → the painting is included on the website.
- **Other tags** (e.g. `flowers`, `mankato`, `Sibley`) → become categories in
  the dropdown menu. Tag a painting with as many as you like.
- **`showcase` tag** → the painting is featured large on the landing page.
- **`## Description`** → the text shown when someone clicks a painting. Leave it
  empty and that section is simply hidden.
- **The `![[...]]` embed** → tells the build which image to use. If a folder has
  more than one image, the embedded one wins.
- **`date`, `medium`, `dimensions`** → shown in the painting's detail view; any
  left blank are hidden.

---

## Categories

Category labels in the dropdown come from your tags. Friendly display names
(e.g. `MemoryPlace` → "Memory Places") are mapped inside `index.html`. If you
add a new tag and want a nicer label, ask Claude to add it to the `catNames`
list.

---

## What's in this folder

| File / folder      | What it is                                               |
|--------------------|----------------------------------------------------------|
| `index.html`       | The entire website (HTML, CSS, JavaScript in one file).  |
| `paintings.json`   | Generated data: one entry per painting. Do not hand-edit.|
| `images/`          | Web-sized (600px) copies of each painting.               |
| `README.md`        | This file.                                               |

---

## Image handling

- Source images stay full-resolution in Obsidian; only **600px** copies are
  published, to keep the site fast and discourage high-res copying.
- Basic right-click / drag protection is enabled in the browser.
- This is a deterrent, **not** true copy protection — anything visible in a
  browser can ultimately be captured. The small size is the real safeguard.

---

## Backups

Created during setup, in `/Users/peterj1/Documents/`:

- `obsidian-artwork-md-backup-YYYYMMDD-HHMMSS.tar.gz` — all painting notes
- `washington&broad-original-backup.jpeg` — pre-rotation original

---

## Publishing details (for reference)

- Hosted free via **GitHub Pages** from the `main` branch.
- Pushing to GitHub auto-publishes; the live site updates within about a minute.
- Authentication uses an **SSH key** already set up on this Mac, so pushes need
  no password or token.
- If the browser shows an old version, do a hard reload (**Cmd + Shift + R** in
  Firefox) — the data file is cache-busted, but the page itself may be cached.
