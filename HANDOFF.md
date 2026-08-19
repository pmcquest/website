# Handoff: Patrick McQuestion academic website

Context for picking this project back up in a new session. Open `g:\My Drive\Academia\Website` as the VS Code workspace root.

## What this is

A Quarto-built academic website (Home/About, Research, Publications, Teaching, Vitae), replacing a Google Sites page. Styled in Notre Dame navy/gold (`styles.scss`). Deployed as a static site to GitHub Pages.

- **Repo:** https://github.com/pmcquest/website (branch `main`)
- **Live URL:** https://pmcquest.github.io/website/ — **not live yet**, see "Open item" below.
- **Local path:** `g:\My Drive\Academia\Website`

## How the project is wired

- `_quarto.yml` — project config. `output-dir: docs` means rendered HTML goes to `docs/`, which is committed to git and served directly by GitHub Pages (Settings → Pages → source = branch `main`, folder `/docs`).
- `index.qmd`, `research.qmd`, `publications.qmd`, `teaching.qmd`, `vitae.qmd` — the five pages. Edit these, not anything in `docs/` directly (that's generated output).
- `images/headshot.jpg` — real photo, already wired into `index.qmd`. `images/Patrick McQuestion.jpg` is the original upload, kept locally but gitignored (duplicate).
- `files/McQuestion-CV.pdf` — the downloadable CV linked from `vitae.qmd`. This is a **copy**, not a source file — see below.

## Source of truth for personal content

Bio, CV, research statement, and teaching statement all live centrally at **`g:\My Drive\Academia\me\`** (moved there from the Dissertation folder specifically so it's reusable across the website, grant applications, and job applications — not dissertation-specific).

- `Academia\me\cv.qmd` — **master CV source**, Quarto/Typst. Renders to a full, unabridged CV PDF (includes phone number and references — appropriate for actual applications, not for the public site).
- `Academia\me\Bio.md`, `CV-academia.md`, `McQuestion-ResearchStatement_021526.md`, `McQuestion-Teaching.md` — source material the website's `index.qmd`/`research.qmd`/`teaching.qmd` content was adapted from.
- `Academia\me\McQuestion-CoverLetterND.md` — a speculative/template cover letter (references publications as if already out that are actually still under review) — not a factual source, don't pull from it.
- `Academia\me\Interviews_McQuestion.md` — job-market department-research notes, unrelated to the website.
- `Academia\me\old\` — archived original `.docx`/`.pdf` versions.

**Important asymmetry:** the website's Vitae *page* (HTML, in `vitae.qmd`) is a deliberately trimmed public version — no phone number, no references' contact info. The downloadable PDF (`files/McQuestion-CV.pdf`) is the full CV from `Academia\me\cv.qmd`, unabridged. They are not auto-synced; update both by hand.

## How to update content

**Website pages (bio text, publications list, etc.):**
1. Edit the relevant `.qmd` in `g:\My Drive\Academia\Website\`
2. `cd "g:/My Drive/Academia/Website" && quarto render`
3. `git add -A && git commit -m "..." && git push`

**CV PDF:**
1. Edit `g:\My Drive\Academia\me\cv.qmd`
2. `cd "g:/My Drive/Academia/me" && quarto render cv.qmd --to typst`
3. `cp cv.pdf "g:/My Drive/Academia/Website/files/McQuestion-CV.pdf"`
4. Re-render and push the website as above (the PDF counts as a content change).

There's no automation tying these together yet — if this becomes a frequent enough workflow, a small script to do steps 2–4 in one shot would be a reasonable next step (not built, since it hasn't been asked for).

## Open item

**GitHub Pages is not yet enabled** — last checked, https://pmcquest.github.io/website/ returned 404. To finish: go to https://github.com/pmcquest/website/settings/pages, set Source to "Deploy from a branch," Branch `main`, folder `/docs`, Save. Should go live within a couple minutes.

## Environment notes

- Quarto 1.9.37 and git are installed and confirmed working. No GitHub CLI (`gh`) installed — repo/Pages setup was done via plain `git` + the GitHub web UI.
- Typst (bundled with Quarto) renders the CV PDF — no LaTeX install needed.
- CV content is current as of the source doc's Feb 17, 2026 date. Re-check it's still accurate before relying on it for an application.
