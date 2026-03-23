# Changelog

All notable changes to this project will be documented in this file.

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
