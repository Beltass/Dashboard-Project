# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

Despite the repo name ("Dashboard-Project"), there is no dashboard application here yet — the
repository currently contains a single self-contained unit, `is-basvuru/` ("job application" in
Turkish), which is a set of three Claude Code **Skills** that automate a personal job-search
workflow for one specific user (Burak Eltaş). There is no build system, package manager, server,
or test suite in this repo.

All skill instructions, reference data, and code comments are written in **Turkish** — keep new
content in this repo in Turkish to stay consistent, unless the user asks otherwise.

## Repository structure

```
is-basvuru/.claude/skills/
├── setup/SKILL.md              # Prepares the local environment (idempotent, run first)
├── scrape/SKILL.md             # Finds job postings on LinkedIn / kariyer.net
└── apply/
    ├── SKILL.md                 # Scores a posting and prepares application materials
    └── references/
        ├── profil.md            # Single source of truth for all factual CV content
        ├── cv-sablonu.tex        # LaTeX CV template (personalized per application)
        └── reviewer-kriterleri.md  # 100-point ATS/recruiter fit-scoring rubric
```

`basvurular/` (application output directory) and `basvurular/takip.csv` (application tracking log)
are created at the repo root by the `setup` skill on first run — they do not exist until then.

## The three skills and how they chain together

1. **`setup`** — Verifies `profil.md` exists and is non-empty, verifies `cv-sablonu.tex` exists,
   checks that `pdflatex` (or `tectonic`) is on `PATH`, and creates `basvurular/` plus
   `basvurular/takip.csv` (header: `tarih,sirket,unvan,ilan_url,kaynak,uygunluk_puani,durum,not`)
   if missing. Run this before `scrape` or `apply`, or whenever the environment seems broken. It
   makes no network calls and submits nothing.

2. **`scrape`** — Reads target titles/location from `profil.md` §"Hedef Rol Anahtar Kelimeleri",
   searches **only LinkedIn and kariyer.net** public search-result pages (no login-wall bypass, no
   CAPTCHA solving, no automated credentials — reuse an already-logged-in browser session if
   available), dedupes against `basvurular/takip.csv` by `ilan_url`, and presents a shortlist
   table. It does not score fit and does not submit anything.

3. **`apply`** — Given one posting (from `scrape` output or a pasted URL/text):
   - Scores fit 0–100 against `reviewer-kriterleri.md`'s rubric (title/seniority 20, keyword match
     20, experience scope 15, location/work-mode 15, industry 10, language requirement 10,
     education 5, concrete requirements 5).
   - **< 50**: no draft is produced; logs `durum=atlandi` to `takip.csv` with a reason.
   - **50–69**: shows the fit rationale and asks the user whether to proceed.
   - **≥ 70**: proceeds automatically.
   - Copies `cv-sablonu.tex` to `basvurular/<sirket-kisa-ad>-cv.tex` and personalizes **only**
     `\profilOzet` (2–4 sentences), `\hedefUnvan`, and the ordering of the sidebar
     `YETKİNLİKLER` list (no items added/removed). Experience, education, certifications, and
     awards sections must never be touched.
   - Compiles with `pdflatex -interaction=nonstopmode -halt-on-error basvurular/<sirket>-cv.tex`.
   - Writes a 3–5 sentence, posting-specific cover note/LinkedIn message using concrete
     achievements from `profil.md`.
   - Appends a row to `basvurular/takip.csv` (`durum=taslak-hazir`; user later confirms actual
     submission to update it to `basvuruldu`).
   - **Never submits anything** — no form filling, no "Easy Apply" clicks, on any platform. The
     user reviews the generated PDF/draft and applies manually.
   - If `takip.csv` already has a row for the same `ilan_url`, ask the user before generating
     another draft.

## Key invariants to preserve

- **`profil.md` is the only source of truth** for facts (numbers, titles, dates, achievements).
  `apply` may rephrase/reorder/shorten text for a specific posting but must never invent or
  inflate figures, titles, or dates found there.
- **`cv-sablonu.tex` content outside `\profilOzet` / `\hedefUnvan` / skills-list ordering must
  match `profil.md` verbatim** — treat any drift between the two files as a bug to flag to the
  user.
- **`babel[turkish]` must never be added** to `cv-sablonu.tex`. It's intentionally omitted: when
  loaded together with `hyperref` + `enumitem`, it breaks compilation on the second colored bullet
  in an `itemize` environment ("Missing \endcsname inserted") — a verified package conflict noted
  in comments at the top of both `cv-sablonu.tex` and `setup/SKILL.md`. `utf8`/`T1` already handle
  Turkish characters (ç, ğ, ı, İ, ö, ş, ü) correctly; the only loss is Turkish hyphenation rules,
  which don't matter for short CV bullets.
- Required LaTeX packages for the template: `xcolor`, `amssymb`, `paracol`, `enumitem`,
  `changepage`, `eso-pic`, `hyperref` (most ship with `texlive-latex-extra`).
- No skill here automates form submission, "Easy Apply" clicks, login-wall bypass, or CAPTCHA
  solving — this is a hard boundary across all three skills, not just a style preference.

## Common commands

Compile a personalized CV (from repo root):
```bash
pdflatex -interaction=nonstopmode -halt-on-error basvurular/<sirket>-cv.tex
```

Check for a LaTeX engine:
```bash
command -v pdflatex || command -v tectonic
```

Install LaTeX on Debian/Ubuntu if missing:
```bash
apt-get install -y --no-install-recommends texlive-latex-base texlive-latex-recommended texlive-latex-extra
```

There is no lint/test/build tooling beyond the LaTeX compile step above.
