# AGENTS.md — Repository Guide for AI Coding Agents

## 1. Purpose of This Repository
This repository (`VisionXAiRy/novathesis`) is a fork of the upstream [`joaomlourenco/novathesis`](https://github.com/joaomlourenco/novathesis) LaTeX thesis and dissertation template (currently synced with upstream **v8.5.0**).

The sole purpose of this fork is to provide updated, institutional-compliant support for the **Polytechnic University of Setúbal (*Universidade Politécnica de Setúbal* — UNIPS)** and all of its schools under `novathesisFiles/Schools/unips/`, while keeping **all upstream code intact**.

### Golden Rule for Agents
- **Do NOT modify upstream core files (`novathesis.cls`, `novathesisFiles/StyFiles/*`, `novathesisFiles/Strings/*`, or other universities under `novathesisFiles/Schools/*`)** unless explicitly instructed by the user or pulling new changes from `upstream` (`https://github.com/joaomlourenco/novathesis.git`).
- All UNIPS-specific customizations live strictly inside `novathesisFiles/Schools/unips/` (plus the school list in `0-Config/1_novathesis.tex`, `.Build/schools.conf`, and the fork note at the top of `README.md`).

---

## 2. Supported UNIPS Schools (`unips/*`)
All UNIPS schools share the base university configuration and cover layout defined in `novathesisFiles/Schools/unips/unips-defaults.clo` (matching the official ESTSetúbal/UNIPS report cover specification, with NOVA FCT defaults for the remainder of the document) and override the school name and vector PDF logo in their respective `<school>` subfolder:

| School Option (`\ntsetup{school=...}`) | Folder / Config File | Institution / School Name (PT / EN) | Vector Logo Asset |
| :--- | :--- | :--- | :--- |
| `unips` or `unips/unips` | `novathesisFiles/Schools/unips/unips--defaults.clo`<br>`novathesisFiles/Schools/unips/unips/unips-unips-defaults.clo` | Universidade Politécnica de Setúbal / Polytechnic University of Setúbal | `unips-logo.pdf` |
| `unips/estsetubal` | `novathesisFiles/Schools/unips/estsetubal/unips-estsetubal-defaults.clo` | Escola Superior de Tecnologia de Setúbal / Setúbal School of Technology | `unips-estsetubal-logo.pdf` |
| `unips/ese` | `novathesisFiles/Schools/unips/ese/unips-ese-defaults.clo` | Escola Superior de Educação / School of Education | `unips-ese-logo.pdf` |
| `unips/esce` | `novathesisFiles/Schools/unips/esce/unips-esce-defaults.clo` | Escola Superior de Ciências Empresariais / School of Business Sciences | `unips-esce-logo.pdf` |
| `unips/estbarreiro` | `novathesisFiles/Schools/unips/estbarreiro/unips-estbarreiro-defaults.clo` | Escola Superior de Tecnologia do Barreiro / Barreiro School of Technology | `unips-estbarreiro-logo.pdf` |
| `unips/ess` | `novathesisFiles/Schools/unips/ess/unips-ess-defaults.clo` | Escola Superior de Saúde / School of Health | `unips-ess-logo.pdf` |
| `unips/essitd` | `novathesisFiles/Schools/unips/essitd/unips-essitd-defaults.clo` | Escola Superior de Sustentabilidade, Indústria e Tecnologias Digitais / School of Sustainability, Industry and Digital Technologies | `unips-essitd-logo.pdf` |

> **Important:** Every `Images/` directory under `novathesisFiles/Schools/unips/` must retain its `DO_NOT_REMOVE_THIS_FILE.txt` marker file; `nt-setup.sty` checks for this file before adding the directory to `\graphicspath`.

---

## 3. Repository Structure
```text
├── template.tex                        # Main LaTeX entry point (do not modify)
├── novathesis.cls                      # Main LaTeX class (upstream)
├── Makefile                            # Build rules (latexmk, bib2gls, biber)
├── 0-Config/                           # User-facing configuration files
│   ├── 0_memoir.tex                    # Low-level memoir class options
│   ├── 1_novathesis.tex                # Main template setup (\ntsetup{doctype=..., school=..., ...})
│   ├── 2_biblatex.tex                  # Bibliography setup
│   ├── 3_cover.tex                     # Cover metadata (title, author, degree, advisers, jury, date)
│   ├── 4_files.tex                     # Frontmatter, chapters, backmatter, and glossary file registration
│   ├── 5_packages.tex                  # User packages and custom macros
│   ├── 6_list_of.tex                   # Ordering of Lists of Figures/Tables/Listings
│   └── 7-aidisclose.tex                # Generative AI disclosure configuration
├── 1-FrontMatter/                      # Abstracts, dedication, acknowledgements, glossary/acronym .bib files
├── 2-MainMatter/                       # Thesis chapters
├── 3-BackMatter/                       # Appendices and annexes
├── 4-Bibliography/                     # BibLaTeX .bib databases
├── 5-Figures/                          # Document figures
├── ESTSetubal-UNIPS/                   # Reference Word (.docx) and PDF (.pdf) templates (PT & EN) for UNIPS/ESTSetúbal
└── novathesisFiles/                    # Core template internals
    ├── ChapStyles/                     # Chapter heading styles
    ├── FontStyles/                     # Font configurations
    ├── Images/                         # Shared template & SDG icons
    ├── Schools/                        # University & school configurations (.clo files + Images/)
    │   └── unips/                      # Polytechnic University of Setúbal (UNIPS) & its 6 schools
    ├── Strings/                        # Localization files (strings-<lang>.ldf)
    └── StyFiles/                       # Core LaTeX/expl3 packages (nt-core.sty, nt-setup.sty, nt-covers.sty, etc.)
```

---

## 4. Architecture & How `novathesis` v8.5.0 Works
1. **School Resolution (`nt-core.sty` & `nt-setup.sty`)**:
   - `\ntsetup{school=<univ>/<school>}` splits the value into `\@UNIV` and `\@SCHL` (and optional third segment `\@DEGREEACR`, defaulting to `\@DOCTYPE`).
   - `nt-setup.sty` loads:
     1. `novathesisFiles/Schools/\@UNIV/\@UNIV-defaults.clo`
     2. `novathesisFiles/Schools/\@UNIV/\@SCHL/\@UNIV-\@SCHL-defaults.clo` (or `novathesisFiles/Schools/unips/unips--defaults.clo` when `school=unips` is given without a slash).
   - Valid LaTeX engines (`pdf`, `lua`, `xe`) and degrees (`phd`, `msc`, `bsc` — where `phd` automatically covers `phdplan`/`phdprop` and `msc` covers `mscplan`) are registered via `\AddValidProcessor` and `\AddValidDegree`.
2. **Data Storage (`memstore.sty`)**:
   - Strings and metadata are stored in property-list stores using `\SetUniversity*`, `\SetSchool`, `\SetMargin*`, `\SetDissertationString`, `\SetAdviserString`, `\SetThesisCover`, etc., and retrieved via `\GetUniversity`, `\GetSchool`, `\GetDocAuthor`, `\GetDocTitle`, `\GetMajorField`, `\GetDegreeName`, etc.
   - Note that `\ntdegreename(<lang>){<Name>}` prepends `DegreeNamePrefix` (`Mestrado em` / `Master in`) to `DegreeName`, while storing the raw `<Name>` in `MajorField`. Therefore, UNIPS `DissertationString` definitions use `\GetMajorField(<lang>)`.
3. **Cover Rendering (`nt-covers.sty`)**:
   - Cover elements are added via `\ntaddtocover[<options>]{<cover-ids>}{<content>}` where `<cover-ids>` are `1-1` (front cover), `2-1` (second cover / jury page), `N-2` (back cover), etc.
   - Elastic vertical spacing uses `vfill=<N>` (not `vspace=<int>`).
   - Because `nt-covers.sty` positions the cover minipage using `\begin{tikzpicture}[remember picture, overlay]`, **at least two LaTeX passes** are required for cover elements to appear at their final page coordinates.

---

## 5. Compiling & Testing
- **Full build via `Makefile` / `latexmk`**:
  ```bash
  make
  ```
- **Testing a specific school / language / doctype override from the command line** (without modifying `0-Config/1_novathesis.tex`):
  ```bash
  pdflatex -shell-escape -interaction=nonstopmode -jobname=test_cover \
    '\def\ntoverride{school=unips/estsetubal,doctype=msc,lang=pt,docstatus=final}\input{template}'
  # Run a second pass so TikZ [remember picture, overlay] resolves page coordinates:
  pdflatex -shell-escape -interaction=nonstopmode -jobname=test_cover \
    '\def\ntoverride{school=unips/estsetubal,doctype=msc,lang=pt,docstatus=final}\input{template}'
  ```
- **Syncing with upstream**:
  ```bash
  git fetch upstream
  git merge upstream/main
  ```
  When merging upstream updates, preserve `novathesisFiles/Schools/unips/`, `AGENTS.md`, the UNIPS entries in `0-Config/1_novathesis.tex` and `.Build/schools.conf`, and the fork header in `README.md`.
