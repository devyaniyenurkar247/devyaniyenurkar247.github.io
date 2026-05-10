# Handoff for Claude Code

This is the static-site output of a Hugo Blox (Wowchemy) academic site for Devyani Yenurkar. The repo is the *built* output that GitHub Pages serves — there are no Hugo source files (no `content/`, no `config.yaml`, no `themes/`). All edits are made directly against the rendered `.html` and `.css` files.

The folder was downloaded as a ZIP from `github.com/devyaniyenurkar247/devyaniyenurkar247.github.io` (no `.git/` directory present), so changes here are local-only until pushed.

## Repo layout

- `index.html` — home page (bio, education, interests, patents, publications)
- `experience/index.html` — experience, education timeline, skills, awards, languages
- `publication/<slug>/index.html` — 8 individual publication pages + `publication/index.html` listing
- `blog/<slug>/index.html` — 4 news posts
- `tags/`, `publication_types/`, `404.html`, etc.
- `css/_entry.<hash>.css` — single Tailwind v4 stylesheet referenced by every HTML page via `<link integrity="sha256-...">` SRI
- `css/_entry.css` — unhashed canonical copy
- `js/`, `dist/`, `media/` — JS, fonts, images

All HTML uses **root-relative absolute paths** (`/css/...`, `/js/...`, `/media/...`). Opening files via `file://` will not work — assets 404. Always test via a local server: `python3 -m http.server 8000` from the repo root, then open `http://localhost:8000`.

## What's been changed (local only, not pushed)

### 1. Custom color palette — "simple and sober"

Appended a `:root { ... }` block to both `css/_entry.css` and `css/_entry.8975dbfb55714d83d3bcb9ef9dfd49acfc4a569580e59833149df73349238ad0.css` overriding the Hugo Blox color CSS variables.

Replaces the default vivid blue/cyan with muted slate primary and warm-stone secondary:

```
--hb-primary-500-rgb: 71 85 105    (slate-600, #475569)
--hb-primary-600-rgb: 51 65 85     (slate-700, #334155)
--hb-primary-700-rgb: 30 41 59     (slate-800, #1e293b)
--hb-secondary-500-rgb: 120 113 108 (stone-500, #78716c)
--hb-secondary-600-rgb: 87 83 78    (stone-600, #57534e)
--hb-secondary-700-rgb: 68 64 60    (stone-700, #44403c)
```

Full ramp (50–900) is defined for both primary and secondary so dark mode works. Search for `Custom palette: simple & sober` in either CSS file to find the block.

### 2. SRI integrity hash recomputed and propagated

Modifying the CSS file invalidated the `integrity="sha256-..."` SRI attribute on the `<link>` tag in every HTML page, which caused Chrome to refuse to apply the stylesheet (page rendered raw HTML). Fixed by:

- Computing new hash: `openssl dgst -sha256 -binary css/_entry.8975dbfb...css | base64`
- Old hash (HTML-encoded form, `+` → `&#43;`): `iXXb&#43;1VxTYPTvLnvnf1JrPxKVpWA5ZgzFJ33M0kjitA=`
- New hash: `sumUwjN1W&#43;34X9eq7xd5BM43GTa6YiOE&#43;eNho4iRBtU=`
- Replaced across all 27 HTML files with a Python script (sed had issues with `&` being interpreted as the matched-pattern reference).

If you re-edit the CSS, you must recompute and propagate the hash again, or strip the `integrity=` and `crossorigin=` attributes entirely.

## What still needs to happen

The local folder works correctly via `localhost:8000` with the new palette, but `https://devyaniyenurkar247.github.io/` (the live GitHub Pages site) is showing a completely unstyled page in Chrome — the same SRI-failure symptom. Two possibilities:

1. The deployed site has a pre-existing CSS/integrity mismatch that was already broken before any local edits.
2. Once the local files are pushed, the live site will match localhost.

**Action needed**: push the local folder to the GitHub repo. Since the user's folder is an extracted ZIP (no `.git/`), the cleanest sequence is:

```bash
cd ~/Downloads/devyaniyenurkar247.github.io-main
git init
git remote add origin https://github.com/devyaniyenurkar247/devyaniyenurkar247.github.io.git
git fetch origin
git checkout -b main
git add -A
git commit -m "Update color palette to slate + warm stone; recompute SRI hashes"
git push -u origin main --force
```

(`--force` is needed because the local tree wasn't checked out from the remote.) Confirm with the user before force-pushing — it will overwrite the remote history. A safer alternative is uploading via the GitHub web UI by dragging files into the repo.

After push, GitHub Pages will rebuild in ~1–3 min. Hard-refresh (Cmd+Shift+R) the live URL to verify.

## Outstanding edits the user previously flagged interest in

These were discussed but not yet acted on — confirm with the user before doing them:

- **Bio update** — current bio is two short sentences. Could be expanded.
- **Add social links** — only email and Google Scholar are present. LinkedIn, ORCID, Twitter, GitHub, ResearchGate could be added. The icons live in `index.html` around the avatar block (search for `mailto:devyaniyenurkar247@gmail.com`).
- **Experience page date bug** — in `experience/index.html`, all three Education entries display only "Present" with no start date (the home page has correct dates). Search for `<time><span class="text-primary-600 dark:text-primary-400 font-medium">Present</span></time>` to find the three broken entries. Correct dates from the home page:
  - PhD in Biomedical Engineering: Dec 2022 – Present
  - M.Pharm in Pharmaceutics: October 2020 – October 2022
  - B.Pharm: August 2016 – September 2020
- **Stale theme metadata** — `<meta name="description">` and `<meta property="og:description">` still contain the Hugo Blox theme placeholder ("The highly-customizable Hugo Academic theme powered by HugoBlox Kit..."). Twitter handle is `@MakeOwnable` (theme default). Both should be replaced with Devyani's actual bio.
- **Empty Featured Publications grid** — the `#papers` section on the home page has the heading but the grid is empty.

## Known constraints / gotchas

- HTML files have very long lines (much of the markup is on a single line). Use `Grep` with `-A`/`-B` context or Python scripts for find-and-replace, not Read on huge ranges.
- Tailwind v4 utility classes are used heavily. The `@layer theme` block at the top of `_entry.css` defines `--color-primary-*` from `--hb-primary-*-rgb`. Overriding `--hb-*-rgb` at `:root` (outside any `@layer`) takes precedence.
- The site has a built-in theme dropdown (Coffee, Dracula, Marine, etc.) that sets `data-theme-pack="..."` on `<html>` and stores in localStorage. The custom palette added above is the *default* (when no theme-pack is selected); the presets still override when picked.
- The site uses `Lora` (headings) and `Source Serif 4` (body) from Google Fonts. Keep these unless the user requests a font change.
- Every JS file also has SRI integrity attributes — don't modify any `js/*.js` file without recomputing its hash too.
