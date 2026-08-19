# Survey Integration Finder

**One survey, many answers.**

A free, open tool to help national disease program, ministries of health and partners
find opportunities to **integrate disease assessments** — either by joining a survey that
is already funded, or by adding assessments to a survey they are about to run.

It also serves as a plain-language directory of standardized survey methods, with links
to the underlying WHO and partner guidance.

---

## How the project fits together

```
Disease Integration Tool/
├─ Integrated_Survey_Catalog_TEMPLATE_v6.xlsx   ← the source of truth. Edit this.
├─ Rebuild site.bat            ← double-click after editing the workbook
├─ build/
│   ├─ convert.py                ← turns the workbook into the site's data files
│   └─ validation_report.txt     ← problems found in the last build
└─ docs/                         ← this folder is what gets published
    ├─ index.html                ← the whole application, one file
    └─ data/
        ├─ data.js               ← used by the site (works without a web server)
        ├─ surveys.json          ← same data, for anyone who wants to reuse it
        └─ facets.json
```

The spreadsheet is the only thing you edit by hand. Everything in `docs/data/` is
generated — never edit those files, they are overwritten on every build.

**Only the Surveys tab feeds the site.** The Diseases tab is reference material kept in
the workbook; disease names shown to users come from the Surveys tab, and all
documentation links come from each survey's `resource_links`. The two tabs do not need
to agree on spelling.  The 'Withheld rare surveys' tab is a repository of surveys that are
currently unfunded or exceedingly rare (e.g. limited to a couple of countries) and hence
not included in this current version.

---

## Updating the content

1. Edit the workbook (the **Surveys** tab: one row per survey).
2. Save and close it.
3. **Double-click `Rebuild site.bat`.** That is the whole build step — it finds Python,
   installs anything missing the first time, regenerates the site's data, and shows you
   the validation report. Close the workbook first; Excel locks the file while it is open.

   If you prefer the command line, the equivalent is:

   ```
   cd build
   python convert.py
   ```

   With no argument it picks the most recently modified `.xlsx` in the project folder,
   so it follows you from v6 to v7 without needing to be told.
   To be explicit: `python convert.py ../Integrated_Survey_Catalog_TEMPLATE_v6.xlsx`

4. Read `build/validation_report.txt`. It lists duplicate IDs, missing required fields,
   diseases not on the Diseases tab, age ranges that are inverted, and any terms that
   fall outside the controlled vocabulary. Vocabulary drift is the one worth watching:
   "Fingerprick blood" and "Finger prick blood" become two separate filter options and
   quietly split your results, so the converter normalizes the common variants and
   reports anything it does not recognize.

5. Open `docs/index.html` in a browser to check it, then commit and push (below).

### Editing conventions

- **Multi-value cells** are separated by semicolons: `AFRO; SEARO`, `School; Community`.
- **`regions`** accepts `ALL` as shorthand for all six WHO regions.
- **`age_min` / `age_max`** are numbers in years (`0.75` = 9 months). These drive the
  age-overlap filter, which is the feature that answers "my survey covers ages 5–14,
  what else could I measure in that group?" A row with no ages will not appear when a
  user filters by age.
- **`resource_links`** are `Title | URL`, one per line (Alt+Enter inside a cell).
- **`notes_internal`** is for curation notes. It is not published to the site.

---

## Publishing to GitHub Pages

One-time setup:

1. Create a free account at [github.com](https://github.com) — use an email address you
   expect to keep long term, since this tool is intended to outlast any one affiliation.
2. Create a new repository, e.g. `survey-integration-finder`, and make it **Public**.
3. Upload this project folder. The simplest route is GitHub Desktop:
   install it, *File → Add local repository*, select this folder, then *Publish repository*.
4. In the repository, go to **Settings → Pages**. Under *Source* choose
   **Deploy from a branch**, pick branch `main` and folder **`/docs`**, and save.
   GitHub Pages only offers the repository root or a folder named exactly `docs` —
   that is why the published folder is called `docs` rather than `site`.
5. After a minute the site is live at
   `https://<your-username>.github.io/<repository-name>/`

To publish updates afterwards: rebuild (step 3 above), then commit and push. GitHub
Pages redeploys automatically within a minute or so.

### A custom domain (optional)

Buy a domain (about $10–15/year), then add it under *Settings → Pages → Custom domain*
and create a CNAME record with your registrar pointing at
`<your-username>.github.io`. GitHub issues an HTTPS certificate automatically.

---


## Status

All records are **drafts pending technical review**. Nothing here should be treated as
WHO-endorsed guidance; the linked source documents are authoritative.
