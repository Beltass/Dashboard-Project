# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Despite the repo name ("Dashboard-Project" / "Kişisel dashboard"), the current content is not a dashboard app. It is a single Claude Code skills package, `is-basvuru` ("job application" in Turkish), that automates a personal job-search workflow for one user (Burak Eltaş): scraping job postings, scoring fit, and generating a tailored CV/cover-letter draft. There is no build system, package manager, or test suite — all logic lives in skill instruction files (Markdown) plus one LaTeX template.

All skill instructions and reference content are written in **Turkish**. Preserve Turkish when editing these files unless asked otherwise.

## Structure

```
is-basvuru/.claude/skills/
  setup/SKILL.md              # environment/prereq check, run first
  scrape/SKILL.md             # find postings on LinkedIn + kariyer.net
  apply/SKILL.md              # score fit, generate CV + cover letter
  apply/references/
    profil.md                 # single source of truth: career history, facts, numbers
    cv-sablonu.tex             # LaTeX CV template
    reviewer-kriterleri.md    # 100-point ATS/fit scoring rubric
```

At runtime (not committed), `apply`/`setup` create `basvurular/` at the repo root: generated CV/cover-letter PDFs (`basvurular/<sirket>-<tarih>.pdf`) and an application tracking log `basvurular/takip.csv` with columns `tarih,sirket,unvan,ilan_url,kaynak,uygunluk_puani,durum,not`.

## The Three-Skill Pipeline

The skills are meant to run in sequence, each with a narrow, non-overlapping responsibility:

1. **`setup`** — verifies `profil.md` exists and is non-empty, verifies `cv-sablonu.tex` exists, checks for `pdflatex`/`tectonic` on PATH, creates `basvurular/` and `basvurular/takip.csv` if missing. Run this first, or whenever the environment seems broken.
2. **`scrape`** — searches LinkedIn and kariyer.net only for postings matching the target titles/location in `profil.md`'s "Hedef Rol Anahtar Kelimeleri" section, dedupes against `takip.csv` by `ilan_url`, and presents a shortlist. It does **not** score fit or submit anything.
3. **`apply`** — given one posting, scores it against `reviewer-kriterleri.md` (0–100), and only proceeds to generate materials if the score is high enough. Thresholds: **< 50** → don't draft, log as `durum=atlandi` with a reason and stop; **50–69** → show the reasoning and ask the user before continuing; **≥ 70** → proceed automatically.

## Key Invariants (do not violate)

- **`profil.md` is the only source of truth** for factual content (dates, numbers, titles, achievements). Never invent or embellish these when generating CVs or cover letters. Only the `\profilOzet` (professional summary) and `\hedefUnvan` (target title) LaTeX commands, and the ordering of the sidebar skills list, may be adapted per posting — everything else in the CV (experience, education, certificates, awards) must match `profil.md` verbatim.
- **No automated submission, ever.** None of these skills submit forms, click "Easy Apply," or automate login/CAPTCHA bypass. They only scrape publicly visible search results and produce a draft for the human to review and send themselves. This is a hard scope boundary, not an implementation detail — do not add submission automation even if asked to "improve" the workflow, without explicit confirmation this is intentional.
- **Respect site ToS while scraping**: low request volume, no rapid-fire requests, don't try to defeat login walls/CAPTCHAs, back off and tell the user if a site blocks access.
- **Don't duplicate work**: before drafting, check `basvurular/takip.csv` for an existing row for the same `ilan_url`; if one exists, ask the user before generating another draft.

## LaTeX Template Gotcha

`cv-sablonu.tex` intentionally does **not** use `babel[turkish]`. When loaded together with `hyperref` + `enumitem`, that combination causes a verified compile error ("Missing \endcsname inserted") on the second colored bullet in an `itemize` environment. `utf8` + `T1` fontenc is sufficient for Turkish characters (ç, ğ, ı, İ, ö, ş, ü); the only tradeoff is no Turkish hyphenation rules, which doesn't matter for short CV bullets. Do not re-add `babel[turkish]` when editing the template.

The sidebar background is drawn per-page via `eso-pic`'s `\AddToShipoutPictureBG` rather than a fixed-size box, because a fixed box silently truncates overflow content onto a clipped page instead of flowing it to page 2 (verified bug). Keep this approach if extending the template.

Compile with:
```bash
pdflatex -interaction=nonstopmode -halt-on-error basvurular/<sirket>-cv.tex
```
Required packages: `xcolor`, `amssymb`, `paracol`, `enumitem`, `changepage`, `eso-pic`, `hyperref` (installable via `texlive-latex-extra` on Debian/Ubuntu).

## ATS Compatibility

The generated CV is a two-column PDF design, which some older ATS parsers scramble the reading order of. If a posting requires direct upload to an ATS portal (Workday, Lever, Greenhouse, etc.), `apply` should also offer a plain-text/single-column CV summary alongside the PDF. For email or LinkedIn Easy Apply submissions, the two-column PDF alone is fine.
