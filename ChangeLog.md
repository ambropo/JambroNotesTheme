# Changelog

All notable changes to this project will be documented in this file.

---

## Version 1.1-dev — *2026-06-29* — Repository cleanup for public release

### Changed
- **Repo layout slimmed to the essentials.** Renamed `DemoNotes.tex` → `Demo.tex` and `DemoNotes.pdf` → `Demo.pdf`. The tracked set is now `notesthemejambro.sty`, `Demo.tex`, `Demo.pdf`, `ChangeLog.md`, `README.md`, `.gitignore`.

### Added
- **`README.md`** — overview, installation, package options, public-API reference, and color list.
- **`.gitignore`** — excludes LaTeX build artifacts (`*.aux`, `*.log`, `*.toc`, `*.lob`, …) and local tooling (`.claude/`), while keeping `Demo.pdf` tracked.

### Removed
- **Build artifacts untracked.** `DemoNotes.aux/.fdb_latexmk/.fls/.log/.out/.synctex.gz/.toc` removed from version control (now regenerated on compile and ignored).
- **AI routing files** `AGENTS.md` and `CLAUDE.md` removed from the repo; `.claude/` untracked (local config kept on disk, no longer published). All remain recoverable from Git history.

---

## Version 1.1-dev — *2026-06-29* — Publication-style table demo + tighter `\notesize` leading

### Changed
- **`notesthemejambro.sty`** — *`\notesize` leading set solid (9.0pt → 8.5pt).* `\notesize` is the theme's standard small-print size (8.5pt) used for table notes; its baseline was `\@setfontsize\notesize{8.5}{9.0}`. Set solid (`{8.5}{8.5}`) so multi-line notes form a compact block visually subordinate to the table. Comment block updated with the rationale. (On its own this is cosmetic and near-invisible — the leading a note actually receives depends on where `\par` fires; see the `DemoNotes.tex` fix below.)
- **`DemoNotes.tex`** — *both demo tables now use one publication-style `minipage` pattern, with correct note leading.* Section VI.A's table (formerly a generic "externality types" table: plain `table`, single-line headers) is now a sign-restrictions table; Section VI.B's "Model Parameters" table was wrapped in the same layout. The shared pattern: small-caps `\caption{\scshape{…}}` above a fixed-width `\begin{minipage}[b]{…\textwidth}`, a centred `\small` `tabularx{\textwidth}{lYY}`, and a `\notesize` `{\scshape Note.}` below. VI.A adds `\shortstack` two-line headers and a long note that demonstrates wrapping; VI.B keeps its `\rowcolor` shading and now carries a note that cross-references VI.A via `Table~\ref{tab:SR}`. The VI.A `democode` source listing was updated to match. The subsections still showcase the `Y` column type and `\notesize` (VI.A's note was changed from the template's `\scriptsize` to `\notesize` to keep that feature demonstrated).

### Fixed
- **`DemoNotes.tex`** — *table notes were rendering at body leading (~13.6pt), not `\notesize` leading.* Each note was written `{\notesize … text.}` with the closing brace **before** the paragraph break. LaTeX fixes a paragraph's `\baselineskip` at the `\par`, but the group had already closed and reverted the baseline to the surrounding 11pt body value — so the note's lines were widely spaced regardless of `\notesize`'s 8.5pt setting (this is why the `.sty` leading change above had no visible effect on its own). Fix: force the paragraph to end inside the group by appending `\par` before the closing brace (`… text.\par}`). Before → after: note leading drops from ~13.6pt to 8.5pt; the note now renders as a tight block. *Caveat for users:* the same `{\notesize …}` idiom anywhere will revert to body leading unless a `\par` is inside the group.

*Verification: `latexmk -pdf DemoNotes.tex` → exit 0, 11 pages, 0 errors, no undefined/multiply-defined references; `Table~\ref{tab:SR}` resolves to "Table 1". PDF page 8 inspected at 150 dpi: both notes now render with visibly tight (solid 8.5pt) leading and no ascender/descender collisions, distinctly closer than the prior render. (The IDE statically flags a "duplicate `tab:SR`" because the same `\label` appears in the VI.A `listing only` `democode` block; that copy is never executed — confirmed by the clean compile.)*

---

## Version 1.1-dev — *2026-06-28* — Match the "List of Boxes" title to "Contents"

### Fixed
- **`notesthemejambro.sty`** — *"List of Boxes" title was larger and darker than "Contents".* The TOC title font was styled (`\cfttoctitlefont` → `\Large\bfseries\firatitle\color{title}`) but the List-of-Boxes title kept the tocloft default (`\Huge\bfseries`, body font, black), so the two front-matter headings did not match. Root cause: `\newlistof{boxes}{lob}{...}` names the title-font hook after the list *extension* (`\cftlobtitlefont`), not the type (`boxes`) used by the entry hooks (`\cftboxesindent` etc.) — so a `\cftboxestitlefont` override would silently target a non-existent macro. Fix: `\renewcommand{\cftlobtitlefont}{\Large\bfseries\firatitle\color{title}}`, identical tokens to `\cfttoctitlefont`. Before → after: "List of Boxes" was `\Huge` black; it is now `\Large` dark-blue Fira Sans bold, matching "Contents". No change to entries or to `DemoNotes.tex` (the feature is already exercised by `\listofboxes`).

*Verification: `latexmk -pdf DemoNotes.tex` → exit 0, 0 errors, 0 undefined references. PDF page 2 inspected: "List of Boxes" title now renders at the same size, weight, font, and color as the "Contents" title on page 1.*

---

## Version 1.1-dev — *2026-06-28* — Clean paragraph cross-references (`\para` + `\label`/`\ref`)

### Fixed
- **`notesthemejambro.sty`** — *trailing dot leaked into `\ref`.* `\theparanum` was `\arabic{paranum}.` (dot baked into the counter representation). Since `\para` calls `\refstepcounter`, that dot was copied into `\@currentlabel`, so `\ref{para:...}` resolved to e.g. "2." — reading like a sentence break mid-text ("paragraph 2. restores …"). Fix: `\theparanum` is now the bare number `\arabic{paranum}`, and `\para` appends the dot at typeset time (`\theparanum.\quad`). Before → after: paragraph-start display is unchanged ("1.  2.  3. …"); `\ref` to a labelled paragraph now returns a clean "2". Backwards compatible for display; only `\ref` output changes (to the correct form).

### Added
- **`DemoNotes.tex`** — *labelling/cross-referencing example in Section VII (Paragraph Numbering).* The second numbered paragraph is now `\label{para:bind}`; a `democode` snippet shows the `\para\label{...}` / `\ref{...}` idiom; a fourth numbered paragraph builds on the labelled one via `\ref` ("Building on the assumption introduced in paragraph~2 …"); and the closing `notebox` was rewritten to demonstrate a `\ref` resolving *inside* an environment (was a vaguer meta-comment about paragraphs 1–3). Verified: `latexmk -pdf DemoNotes.tex` → exit 0, 10 pages, 0 errors, no undefined/multiply-defined references; all three `\ref`s render "2".

---

## Version 1.1-dev — *2026-06-28* — Align demo with the VAR Toolbox Handbook (colors, title/TOC, lettered appendix, grey demo source)

### Changed
- **`notesthemejambro.sty`** — *heading color → dark blue.* Section and subsection headings, the TOC "Contents" title, TOC section entries (`\cftsecfont`/`\cftsecpagefont`), and `\tocheading` now use the dark-blue `title` color instead of the lighter `note` blue. The `title` color itself was retuned `RGB{7,40,91}` → `RGB{0,0,90}` to match the VAR Toolbox Handbook's `section`/`title` color. Before → after: headings and references were both `note` blue (indistinguishable); headings are now dark blue while references (hyperlinks/citations, still `linkcolor=citecolor=urlcolor=note`) remain the lighter `note` blue — exactly the handbook's dark-title / light-reference split. `note` is unchanged (`RGB{49,79,179}`) and still drives links and `note!10`/`note!40` box shading.
- **`notesthemejambro.sty`** — *`\backmatter` now letters appendices.* `\backmatter` switches `\thesection` to `\Alph` (A, B, …) and `\thesubsection` to `\Alph{section}.\arabic{subsection}` (A.1, A.2, …) and resets both counters, matching the handbook's `\appendix` convention. Before → after: an appendix section was Roman (restarted at "I"); it is now lettered (restarted at "A"). Hyperref anchors stay unique (`\theHsection` → `back.\Alph{section}`). **BREAKING (cosmetic):** existing documents using `\backmatter` will see appendix labels change from Roman to letters; cross-references via `\ref` follow automatically.
- **`DemoNotes.tex`** — *demo source listings → grey `code` look.* The demo-only `democode` environment was redefined from a blue-text framed `fancyvrb` `Verbatim` to a `\newtcblisting` on the light-gray `codegray` background with the monochrome `jambrocode` style — i.e. the same look as the theme's `code` environment, so shown source and rendered result share one visual style.
- **`DemoNotes.tex`** — *title size/color.* The title block now uses `\huge` for the main line (handbook title size) and the dark-blue `title` color (was `\color{BoEblue}` at default `\LARGE`).
- **`DemoNotes.tex`** — *appendix prose updated* for the new lettering and a stale cross-reference fixed: "restarts at I … follows Section VIII" → "lettered A … follows Section X" (the appendix actually follows Section X "Front and Back Matter").

### Added
- **`DemoNotes.tex`**: an introductory sentence under **Section IX (Package Options)** stating what the section covers — the two load-time options `wiggle=<n>` and `hidememo` — before the per-option subsections.

*Verification: `latexmk -pdf DemoNotes.tex` → exit 0, 10 pages, 0 errors, no new style warnings (2 pre-existing mild Underfull \hbox only, badness ≤3138). PDF inspected: title `\huge` dark blue; "Contents" and TOC entries dark blue at `\Large`; appendix renders as "A Appendix: Quick Reference"; `democode` blocks render grey/monochrome throughout; Section IX intro sentence present. Note: the List of Boxes heading was left untouched per request and remains black — see report.*

---

## Version 1.1-dev — *2026-06-28* — Bold section entries in the TOC

### Changed
- **`notesthemejambro.sty`**: TOC **section** entries are now bold. Added `\bfseries` to both `\cftsecfont` and `\cftsecpagefont` (TOC AND FRONT/BACK MATTER block). Before → after: top-level section titles and their page numbers in the table of contents were regular weight; they are now bold, matching the body `\section` headings (`\bfseries\firatitle\color{note}`) and standard LaTeX practice. Subsection entries (`\cftsubsecfont`) are intentionally left non-bold to preserve the visual hierarchy. Backwards compatible: cosmetic TOC change only. Verified: `latexmk -pdf DemoNotes.tex` → exit 0, 10 pages, 0 errors/warnings; TOC page rendered and inspected (section lines bold, subsection lines regular).

---

## Version 1.1-dev — *2026-06-28* — Source Sans Pro body (+ load-order fix)

### Changed
- **`notesthemejambro.sty`**: body text is now **Source Sans Pro** (`\RequirePackage[default,scaled=1.0]{sourcesanspro}`), replacing the intended Helvetica. Headings remain **Fira Sans** via `\firatitle`. Updated the top-of-file DESCRIPTION and the `--- Font ---` comments accordingly, and the `DemoNotes.tex` "Body Text" subsection prose (Helvetica → Source Sans Pro).

### Fixed
- **`notesthemejambro.sty`** — *body font never actually applied.* The previous block loaded `helvet`, set `\renewcommand{\familydefault}{\sfdefault}`, then loaded `FiraSans` **after**. In the pdfLaTeX/type1 path `FiraSans.sty` renews `\sfdefault` to the Fira family *unconditionally* (its `sfdefault` option only gates whether `\familydefault` is also changed). Because `\familydefault` was pointed at the live `\sfdefault` macro, the body silently resolved to **Fira Sans**, not Helvetica — confirmed via `pdffonts` on the previous build (only `FiraSans-*` embedded, no `phv`). Fix: load `FiraSans` **first**, then `sourcesanspro` with `[default]` **last** (its `[default]` `\edef`s both `\sfdefault` and `\familydefault` to the Source Sans Pro family), and **removed** the `\renewcommand{\familydefault}{\sfdefault}` line that re-exposed the clobber. Verified: `pdffonts DemoNotes.pdf` now embeds `SourceSansPro-Regular/Bold/It` for body and `FiraSans-*` for headings.

### Added
- **`notesthemejambro.sty`**: `\setlength{\emergencystretch}{3em}` in the FORMAT AND LAYOUT block. The wider Source Sans Pro body stranded long inline `\verb` spans (command/environment names) past the margin, producing 3 Overfull `\hbox` warnings in `DemoNotes.tex`. `\emergencystretch` lets TeX add interword stretch as a last resort, clearing the overfulls (2 mild Underfull lines remain, badness ~3000 — loose spacing only, nothing in the margin). Backwards compatible: only affects lines that would otherwise overflow.

*Verification: `latexmk -pdf DemoNotes.tex` → exit 0, 10 pages, 0 errors. The earlier "Helvetica body" entry below was therefore never visually realised; this entry supersedes its font claim.*

---

## Version 1.1-dev — *2026-06-28* — Demo shows source code for every feature

### Added
- **`DemoNotes.tex`**: a demo-only `democode` verbatim environment (defined in the preamble via `fancyvrb`'s `\DefineVerbatimEnvironment`, not part of the theme) that prints the LaTeX source of a feature as a small framed listing. Used to present **code → rendered-result** pairs so readers can copy exact usage.

### Changed
- **`DemoNotes.tex`**: made usage explicit across the whole showcase. Each public feature now shows its source before its rendered output. Specifically: `\hand`/`\handbold`, `\lapis` (with per-variant `\verb|...| → result` lines), `\lapisbox`, `\lapisboxeq` (display and inline), `label=` + pencil-arrow `tikzpicture`, `\NB`, `\memo` (including the preamble `\memouser`/`\memocolor` defaults), `notebox`, `\E`, the `tabularx` `Y` column with `\notesize`, `\rowcolor`, `\para`, `\jarrow*` (noting the trailing `\ ` to preserve the following space), and `\marker` + overlay `tikzpicture`. Before → after: the demo previously showed only rendered output for most features; it now shows the typed source alongside.

### Fixed
- **`DemoNotes.tex`**: avoided reintroducing the known `OMS/cmtt` font-substitution warning — an intermediate draft used `\texttt{\{...\}}` (literal braces in `\texttt` are fetched from the OMS encoding, which `cmtt` lacks); switched to `\verb|{...}|`. Verified the final build is warning-free.

*Note: this entry is independent of the Helvetica/Fira Sans font change below; the usage examples do not depend on the body font.*

---

## Version 1.1-dev — *2026-06-28* — Helvetica body, Fira Sans headings

### Changed
- **`notesthemejambro.sty`**: replaced the document font. Body text is now **Helvetica** (`\RequirePackage[scaled]{helvet}` with `\familydefault` set to `\sfdefault`) instead of Cabin. Section and subsection headings now use **Fira Sans** via a new `\firatitle` switch (`\fontfamily{FiraSans-OsF}\selectfont`), replacing the previous `\sffamily` in the `\titleformat` blocks. `FiraSans` is loaded without `sfdefault`, so it supplies the heading family without overriding the Helvetica body. Before → after: whole document was Cabin; now body = Helvetica, headings = Fira Sans.
- **`DemoNotes.tex`**: updated the "Body Text" subsection prose to describe Helvetica body + Fira Sans headings (it previously stated the font was Cabin).
- **`notesthemejambro.sty`**: refreshed the top-of-file DESCRIPTION and the heading-format comments to reflect the new typography.

### BREAKING
- The default typeface changed for **all** documents using this style (Cabin → Helvetica body / Fira Sans headings). Documents relying on the previous Cabin appearance will render differently. No option toggle is provided: the new fonts are unconditional. To restore the old look, revert the `--- Font ---` block to `\RequirePackage[sfdefault]{cabin}` and the `\titleformat` blocks to `\sffamily`.

---

## Version 1.1-dev — *2026-03-23*

### Added
- **`DemoNotes.tex`**: added explicit `hidememo` option coverage so both public package options now appear in the demo harness.

### Changed
- **`AGENTS.md`**: synced the maintenance instructions with the real repo filenames (`notesthemejambro.sty`, `DemoNotes.tex`) and the live annotation command (`\memo`).
- **`notesthemejambro.sty`**: bumped the working-tree package header to `v1.1-dev` and refreshed the top-of-file API summary so option defaults and public-command docs match the live code.
- **`DemoNotes.tex`**: synced the demo title with `v1.1-dev`, corrected the documented default `wiggle` amplitude from `0.8pt` to `0.4pt`, and clarified that `\marker` wraps visible text rather than creating invisible content.
- **`notesthemejambro.sty`**: switched the dependency block from `\usepackage` to `\RequirePackage` for package-style consistency with `beamerthemejambro.sty`, and renamed the internal wiggle-amplitude macro from `\jambronoteswiggleamp` to `\jambrowiggleamp`.

### Fixed
- **`notesthemejambro.sty`**: internalised the old `pencilboxstyle` helper as `jambro@pencilboxstyle`, documented the custom `\notesize` definition more accurately, and tightened several comments so they explain intent rather than restating LaTeX syntax.
- **`DemoNotes.tex`**: replaced several `\texttt{\textbackslash ...}` snippets with safer `\verb|...|` or plain-text forms and shortened the labeled-box explanation to remove the demo's existing `cmtt` font warnings and overfull line.

### Removed
- **`notesthemejambro.sty`**: removed the unused `patterns` and `arrows` TikZ libraries and dropped the unused `snake` style from the live API surface.

---

## Version 1.1 — *2026-03-23*

### Added
- **`notesthemejambro.sty`**: `\NeedsTeXFormat{LaTeX2e}` and `\ProvidesPackage{notesthemejambro}` guards at top of file — required by LaTeX package conventions; previously absent.
- **TikZ styles** (ported from `beamerthemejambro.sty`):
  - `arrow/.style`: pencil-effect directed line (`->`) for use in `tikzpicture` overlays.
  - `snake/.style`: wavy squiggle line (built-in TikZ snake decoration) for emphasis marks.
- **Commands** (ported from `beamerthemejambro.sty`):
  - `\marker{name}{text}`: invisible `remember picture` anchor node for arrow targeting.
  - `\jarrowup`, `\jarrowdown`, `\jarrowright`, `\jarrowleft`: inline pencil arrows, baseline-aligned. Accept `color`, `len`, `lw`, `pad` options via pgfkeys `/jarrow` family.
  - `\jarrowu`, `\jarrowd`, `\jarrowr`, `\jarrowl`: shorthand aliases for the above.
- **`DemoNotes.tex`**: added Section VII demonstrating `\marker`, `\jarrow*`, and the `arrow` style.

### Fixed
- `ChangeLog.md` v1.0 entry: corrected file name (`JambroNotes.sty` → `notesthemejambro.sty`), corrected command name (`\ACB` → `\memo`), corrected demo file name (`template.tex` → `DemoNotes.tex`).

---

## Version 1.0 — *2026-03-23*

### Added
- **`notesthemejambro.sty`**: Initial extraction of the preamble from `JambroNotes.tex` into a standalone reusable style file. Contains all packages, color definitions, custom commands, TikZ styles, and formatting settings previously embedded inline.
- **`DemoNotes.tex`**: Minimal starter/feature-showcase document using `\usepackage{notesthemejambro}`.
- **`AGENTS.md`**: AI agent instructions for maintaining the style repo.
- **Colors**: `tomato`, `note`, `BoEacqua`, `BoEorange`, `BoEpurple`, `BoEgold`, `BoEgray`, `BoEblue`, `BoEred`, `title`, `cobalt`, `carandache`, `gold`.
- **Environments**: `notebox` — a breakable tcolorbox with light `note`-blue background, no frame.
- **Commands**:
  - `\NB{text}`: Inline todo note in `note` color.
  - `\E`: Shorthand for `\mathbb{E}`.
  - `\memo[user=u,color=c]{text}`: Draft annotation in handwriting font with underlined username prefix. Supports per-call `user=` and `color=` overrides; suppressed by `hidememo` option.
  - `\hand`: Switches to Augie handwriting font.
  - `\handbold{text}`: Bold Augie handwriting via PDF stroke-width literal.
  - `\lapis{text}` / `\lapis[color]{text}`: Pencil-effect underline via TikZ sketch decoration. Default color: `carandache`.
  - `\lapisbox{text}` / `\lapisbox[color=...,label=...]{text}`: Pencil-effect box around inline text. Default color: `gold`. Supports named nodes (`label=`) for cross-reference arrow targeting.
  - `\lapisboxeq{expr}` / `\lapisboxeq[color=...]{expr}`: Pencil-effect box for display math; wraps content in `$\displaystyle...$`.
  - `\para`: Auto-numbered paragraph prefix (Arabic numerals with trailing period).
  - `\notesize`: 8.5pt font size for table annotations.
- **TikZ**: `sketch` decoration (hand-drawn line effect); `pencil`, `nstyle`, `tstyle`, `tpstyle`, `pencilboxstyle` styles.
- **Section formatting**: Roman numerals for `\section`, Roman.Alpha for `\subsection`; uppercase sans-serif headings in `note` blue.
- **Table**: `Y` column type (centered `tabularx` column); `\arraystretch` set to 1.15.

### Changed
- `JambroNotes.tex` preamble replaced with two lines: `\documentclass[11pt,a4paper]{article}` and `\usepackage{notesthemejambro}`.
