# Patrick McQuestion academic website

How to update this site by hand, with no AI assistant — just Quarto and git.

- **Repo:** https://github.com/pmcquest/website (branch `main`)
- **Live URL (English):** https://pmcquest.github.io/website/
- **Live URL (Spanish):** https://pmcquest.github.io/website/es/
- **Local path:** `g:\My Drive\Academia\me\Website`
- **GitHub Pages settings:** Settings → Pages → Source = "Deploy from a branch," Branch `main`, folder `/docs`. Already configured — nothing to redo unless it gets reset.

## What you need installed

- **Quarto** (the `quarto` command on your PATH). Check with `quarto --version`.
- **git**, already configured with push access to `pmcquest/website`.
- No LaTeX needed — the CV PDF renders through Typst, which ships bundled with Quarto.

## The one rule that matters most

**GitHub Pages serves the `docs/` folder, not the `.qmd` source files.** Editing a `.qmd` and pushing it does *nothing* to the live site by itself. Every content change follows the same three steps:

1. Edit the `.qmd` file(s).
2. `quarto render` (regenerates everything in `docs/` from the source).
3. `git add`, `commit`, `push`.

If you skip step 2, the source in the repo and the published site will just be out of sync until the next render.

## Site structure

| Path | What it is |
|---|---|
| `index.qmd`, `research.qmd`, `publications.qmd`, `teaching.qmd`, `vitae.qmd` | The five English pages. Edit these. |
| `es/index.qmd`, `es/research.qmd`, `es/publications.qmd`, `es/teaching.qmd`, `es/vitae.qmd` | The Spanish mirror of each page. See "Bilingual site" below. |
| `docs/` | **Generated output.** Never hand-edit anything in here — `quarto render` overwrites it. This is what GitHub Pages actually serves. |
| `_quarto.yml` | Project config: nav bar, footer, theme, fonts. |
| `styles.scss` | Custom styling (navy/gold accents, layout tweaks, the minima-inspired plain nav/footer). |
| `lang-toggle.html` | A small inline `<script>` (see "Bilingual site" below) included on every page via `_quarto.yml`'s `include-after-body`. |
| `images/`, `files/` | Photo and the downloadable CV PDF. Referenced with relative paths (`images/headshot.jpg`, `files/McQuestion-CV.pdf`) from the root pages, and `../images/...`, `../files/...` from the `es/` pages, since those files aren't duplicated per language. |

## How to update content

```
cd "g:\My Drive\Academia\me\Website"
# ... edit the .qmd file(s) ...
quarto render
git add -A
git commit -m "describe the change"
git push
```

`git status` before committing if you're not sure what changed — `quarto render` regenerates every file in `docs/` even for a one-line edit, so the diff can look bigger than expected. That's normal.

### A Google Drive quirk

This folder lives in a Google Drive–synced directory. Google Drive's sync client sometimes holds a lock on `docs/` right after a render, and a plain `quarto render` will fail with an error like:

```
ERROR: The process cannot access the file because it is being used by another process... remove '...\Website\docs'
```

If that happens, run `quarto render --no-clean` instead. It updates files in place without first deleting the whole `docs/` folder, which sidesteps the lock. It's always safe to use `--no-clean` instead of a plain `quarto render`.

## Bilingual site (English / Spanish)

There's no automatic translation — the `es/` pages are hand-translated `.qmd` files that mirror the English ones, filename-for-filename (`research.qmd` ↔ `es/research.qmd`, etc.). **If you edit an English page's content, the Spanish version doesn't update itself — you have to edit `es/*.qmd` to match by hand.**

Convention used so far, for consistency:

- Prose (bios, descriptions, section intros) — translated.
- Proper nouns, institution names, and official course/talk titles — left in English (e.g., a course literally called *Introduction to Global Politics* stays in English even on the Spanish page), since translating them would misrepresent the actual name.
- Published work titles (articles, chapters, books) — left as published, since that's how they'd need to be cited.
- The CV PDF itself stays English-only; the Spanish Vitae page links to it but flags it as "(PDF, en inglés)."

**The nav bar and footer are not translated in the source** — they're the same English `_quarto.yml` navbar on every page. `lang-toggle.html` is a small script, included on every page, that runs after page load and:

1. Checks whether the current page's URL is under `/es/`.
2. If so, swaps the nav labels (Home → Inicio, etc.) and footer text to Spanish, and rewrites the nav links so they point to the sibling page *within* `es/` instead of the English root (Quarto's own navigation script always resolves the shared navbar against the English root pages, since it has no concept of the Spanish mirror — this rewrite is what keeps you inside the Spanish site when clicking around).
3. Adds an "EN"/"ES" toggle link in the top-right of the nav, pointing at the equivalent page in the other language.

You shouldn't need to touch `lang-toggle.html` unless you rename a page (see below) or add a genuinely new page.

### Adding a new page (both languages)

1. Create `newpage.qmd` at the root and `es/newpage.qmd` with the translation.
2. Add it to `_quarto.yml`'s `website.navbar.left` list (one entry — it applies to both languages, since the nav bar itself isn't duplicated).
3. Add `"NewPage": "NuevaPágina"` to the `navLabels` object near the top of `lang-toggle.html` and `"NewPage": "newpage.html"` to the `pageFile` object, so the toggle script knows how to translate and link it.
4. `quarto render`, check both `docs/newpage.html` and `docs/es/newpage.html`, then commit and push.

## The downloadable CV PDF

The CV is **not** written in this project — it's a separate Quarto/Typst document, and this repo only holds a *copy* of the rendered PDF.

- **Master source:** `g:\My Drive\Academia\me\CV\cv.qmd` (plus its style partial, `cv-typst-header.typ`, in the same folder).
- The PDF here at `files/McQuestion-CV.pdf` is unabridged and English-only, matching the master.

To update it:

```
cd "g:\My Drive\Academia\me\CV"
# ... edit cv.qmd ...
quarto render cv.qmd --to typst
copy cv.pdf "g:\My Drive\Academia\me\Website\files\McQuestion-CV.pdf"
cd "g:\My Drive\Academia\me\Website"
quarto render --no-clean
git add -A
git commit -m "update CV"
git push
```

The website's Vitae page (`vitae.qmd` / `es/vitae.qmd`) is a separately maintained, trimmed HTML summary — no phone number, no reference contact details. It is **not** auto-generated from `cv.qmd`, so if you add something to the CV that should also show on the Vitae page (a new conference talk, a new fellowship), update both by hand, in both languages.

## Checking your work before pushing

Preview the rendered site locally before pushing, so you're not debugging on the live URL:

```
quarto preview
```

This opens a local server (prints a `http://localhost:XXXX/` URL) and live-reloads as you edit. Stop it with Ctrl+C before running `quarto render` again — the preview server can hold a lock on `docs/` the same way Google Drive sync can.

## If something looks broken on the live site after pushing

1. Give it a minute or two — GitHub Pages takes a short while to redeploy after a push.
2. Hard-refresh your browser (Ctrl+Shift+R) or check in a private/incognito window — browsers and GitHub's CDN both cache pages, and a stale cache is the most common cause of "I pushed but nothing changed."
3. If it's still wrong after that, check `git log` to confirm your commit actually made it to `origin/main` (`git push` should say so), and check that `quarto render` completed without errors before you committed.
