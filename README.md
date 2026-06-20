# Work by PKJ — Painting Gallery

A website showcasing Peter K. Johnson's paintings, published with GitHub Pages.

- **Live site:** https://workbypkj.webexplorations.com/
  (also reachable at https://webexplorations.github.io/workByPKJ/)
- **Local copy:** `/Users/peterj1/Documents/workByPKJ/`
- **GitHub repo:** https://github.com/webexplorations/workByPKJ

### Custom domain

The site is served at `workbypkj.webexplorations.com`. This is set up by two
things working together:

1. A **CNAME DNS record** — host `workbypkj` → target `webexplorations.github.io`,
   set to **DNS only** (not proxied). **Important:** `webexplorations.com` DNS is
   managed at **Cloudflare**, *not* BlueHost — add/edit records in the Cloudflare
   dashboard (DNS → Records). A record added at BlueHost has no effect.
2. The **`CNAME` file** in this repo, which tells GitHub Pages to accept that
   hostname.

`webexplorations.com` is **email-only** (ProtonMail); there is no website at the
apex, so Cloudflare's "visitors cannot reach webexplorations.com / www" notices
are expected and harmless. The painting site lives only on the `workbypkj`
subdomain and doesn't touch the email (MX / SPF / DKIM / DMARC) records.

**Last step:** in repo **Settings → Pages**, tick **Enforce HTTPS** (available
once GitHub has issued the certificate). The old `webexplorations.github.io/workByPKJ/`
address 301-redirects to the custom domain, so previously shared links keep working.

### Editing the "About the Artist" text

The bio lives in `index.html` between the `<!-- ABOUT-TEXT-START -->` and
`<!-- ABOUT-TEXT-END -->` comments. Edit the text there (or ask Claude to), each
paragraph wrapped in `<p>…</p>`.

### Other features

- **Sort** control (A–Z / Newest / Oldest) — uses each painting's `date`.
- **Immersive viewer** — full-screen, with zoom & pan (scroll / pinch / drag /
  double-click), prev/next arrows, keyboard (Esc / ← / → / `i`), and a sliding
  Details panel (starts closed). Published full images are 1600px so zoom is
  sharp; the grid uses 500px thumbnails for speed.
- **Light / dark viewer** — a sun/moon toggle in the viewer's top bar switches
  the backdrop behind a painting (the grid stays parchment). The choice is
  remembered per browser.
- **Share links** — each painting has a "Copy link" button; a link like
  `…/#sunflower` opens straight to that painting.
- **Share previews** — the `og-image.jpg` and meta tags control how the link
  looks when posted/messaged. To refresh the preview image, regenerate
  `og-image.jpg` from a painting (ask Claude).

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

> The source painting folders live in the Obsidian vault folder
> **`workByPKJ`** (formerly named `artwork`). `build.py`'s `ARTWORK` path
> points there — update it if the vault folder is ever renamed again.

---

## To update the site

1. **Make your changes in Obsidian** — add new paintings (via
   `process_paintings.command`), write or edit `## Description` text, change
   tags, or adjust which paintings are featured.
2. **Open this folder in Claude Code** and say something like:
   *"Rebuild the painting site from Obsidian and push it."*
3. Claude will:
   - Re-scan the Obsidian artwork folders
   - Resize any new images to 1600px
   - Regenerate `paintings.json`
   - Commit and push to GitHub (the live site updates in ~1 minute)

That's it. The build logic is `build.py` in this folder (kept local / not
published, since it contains machine-specific paths). Running `python3 build.py`
re-scans the vault, regenerates `paintings.json`, resizes new images to 1600px,
records each image's dimensions, and removes images for paintings no longer
tagged `book`.

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

## Formatting your Description text

The `## Description` section supports lightweight **Markdown**, so you can add
links, emphasis, and line breaks. Write it in Obsidian as usual; it renders in
the painting's detail view on the website.

**Links** — square brackets for the words shown, parentheses for the full URL.
Put a link on its own line with a blank line before it:

```markdown
Some text about the painting.

[Read about the poem's origin](http://ambitioninthecity.com/2013/06/07/...)
```

→ shows as a green clickable phrase in the painting's detail view.

**Other formatting that comes through:**

| You write            | You get            |
|----------------------|--------------------|
| `**bold**`           | **bold**           |
| `*italic*`           | *italic*           |
| `[words](https://…)` | a clickable link   |
| `![caption](https://…/image.jpg)` | an embedded image |
| a line break (Return)| a line break       |

Line breaks are preserved, so poems and stanzas keep their shape.

**A caution about images and links to other websites:** linking to or embedding
an image from someone else's site (e.g. a WordPress page) works, but you don't
control it — if they move or delete the file, it breaks silently on your site,
and it adds an outside request each time. For anything you want to keep, prefer
a plain **text link** to the page. If you truly want an image to live in the
note, save a copy into that painting's folder — but note the build currently
publishes only the *main* painting image, so embedding extra local images in a
Description would need a small build change (just ask Claude).

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
| `images/`          | Web-sized (1600px) copies of each painting.               |
| `og-image.jpg`     | Share/link-preview image (1200×630).                     |
| `CNAME`            | Tells GitHub Pages the custom domain.                    |
| `README.md`        | This file.                                               |
| `build.py`         | Build script (local only — gitignored; has machine paths).|

---

## Image handling

- Source images stay full-resolution in Obsidian. Published copies come in two
  sizes: a **500px thumbnail** (`images/thumbs/`) for the fast-loading grid, and
  a **1600px** version (`images/`) for the immersive viewer's zoom. Both are well
  under print resolution, to discourage high-res copying.
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
