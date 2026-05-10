# Handoff for Claude Code

This is the static-site output of a Hugo Blox (Wowchemy) academic site for Devyani Yenurkar. The repo is the *built* output that GitHub Pages serves — there are no Hugo source files (no `content/`, no `config.yaml`, no `themes/`). All edits are made directly against the rendered `.html` and `.css` files.

Live site: `https://devyaniyenurkar247.github.io/`

## Repo layout

- `index.html` — home page (bio, education, interests, patents, publications)
- `experience/index.html` — experience, education timeline, skills, awards, languages
- `publication/<slug>/index.html` — 8 individual publication pages + `publication/index.html` listing
- `blog/<slug>/index.html` — 4 news posts
- `tags/`, `publication_types/`, `404.html`, etc.
- `css/_entry.<hash>.css` — single Tailwind v4 stylesheet referenced by every HTML page
- `css/_entry.css` — unhashed canonical copy
- `js/`, `dist/`, `media/` — JS, fonts, images
- `.nojekyll` — **critical**: prevents GitHub Pages Jekyll from dropping `_entry.*.css` files

All HTML uses **root-relative absolute paths** (`/css/...`, `/js/...`, `/media/...`). Always test via a local server: `python3 -m http.server 8000` from the repo root, then open `http://localhost:8000`.

## What has been done (live on GitHub Pages)

### 1. Custom color palette — "simple and sober"

Appended a `:root { ... }` block to both `css/_entry.css` and `css/_entry.8975dbfb...css` overriding the Hugo Blox color CSS variables. Replaces the default vivid blue/cyan with muted slate primary and warm-stone secondary. Search for `Custom palette: simple & sober` in either CSS file to find the block.

### 2. SRI integrity hash removed

The `integrity=` and `crossorigin=` attributes were stripped from the CSS `<link>` tag in all 27 HTML files. Keeping them caused breakage: the hash was corrupted during a replacement pass and GitHub Pages serves gzip which invalidates any static hash anyway.

**If you re-edit the CSS, you do NOT need to recompute any hash** — just edit and push.

### 3. .nojekyll added

Without this file, GitHub Pages runs Jekyll which silently drops any file whose name starts with `_` — including `_entry.*.css`. This was the root cause of the live site being unstyled.

### 4. Education dates fixed

In `experience/index.html`, all three Education entries now show correct date ranges:
- PhD in Biomedical Engineering (IIT-BHU): Dec 2022 – Present
- M.Pharm in Pharmaceutics (SNDT, Mumbai): Oct 2020 – Oct 2022
- B.Pharm (RTMNU, Nagpur University): Aug 2016 – Sep 2020

### 5. Featured Publications populated

The `#papers` section on the home page now shows 4 papers (was empty). Cards copied from `publication/index.html`.

### 6. Meta descriptions and twitter handle fixed

Replaced Hugo Blox placeholder text across all 44 HTML files. Twitter handle changed from `@MakeOwnable` to `@devyaniyenurkar247`.

## Outstanding edits (not yet done)

- **Bio update** — current bio is two short sentences. Could be expanded.
- **Add social links** — only email and Google Scholar are present. LinkedIn, ORCID, GitHub, ResearchGate could be added. Search for `mailto:devyaniyenurkar247@gmail.com` in `index.html` to find the social links block.

## Known constraints / gotchas

- HTML files have very long lines. Use `grep` with `-A`/`-B` context or Python scripts for find-and-replace, not Read on huge ranges.
- Tailwind v4 utility classes are used heavily. The `@layer theme` block at the top of `_entry.css` defines `--color-primary-*` from `--hb-primary-*-rgb`. Overriding `--hb-*-rgb` at `:root` (outside any `@layer`) takes precedence.
- The site has a built-in theme dropdown (Coffee, Dracula, Marine, etc.) that sets `data-theme-pack="..."` on `<html>`. The custom palette is the default when no theme-pack is selected.
- The site uses `Lora` (headings) and `Source Serif 4` (body) from Google Fonts.
- Every JS file has SRI integrity attributes — don't modify any `js/*.js` file without recomputing its hash too.
- **Never remove `.nojekyll`** — the site breaks without it.
