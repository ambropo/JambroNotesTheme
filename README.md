# Jambro Notes Theme

Ambrogio Cesa-Bianchi's theme for **lecture notes, working notes, and technical handouts** with a light "pencil-sketch" aesthetic: hand-drawn underlines and boxes, sketched arrows, shaded call-outs, numbered boxes and paragraphs, and a clean sans-serif body. Built for economics/quantitative notes but useful anywhere.

A fully worked feature showcase is in **[`Demo.tex`](Demo.tex)** — see the rendered **[`Demo.pdf`](Demo.pdf)**.

> Status: `v1.1-dev`. Backwards-compatible within the 1.x line; breaking changes are flagged in [`ChangeLog.md`](ChangeLog.md).

---

## Getting Started

Drop `jambronotes.sty` next to your `.tex` file (or anywhere in your `TEXMF` tree) and load it:

```latex
\documentclass[11pt,a4paper]{article}
\usepackage{jambronotes}
```

The style loads its own dependencies (`tcolorbox`, `tikz`, `booktabs`, `tabularx`,
`todonotes`, `xcolor[table]`, `setspace`, `tocloft`, `hyperref`, the
`sourcesanspro` and `firasans` fonts, and the Augie handwriting font). A recent
TeX Live (2022 or newer) is recommended. Compile with `latexmk -pdf Demo.tex`;
features that draw arrows to named nodes need **two passes** to resolve.

## Package options

| Option | Default | Effect |
| --- | --- | --- |
| `wiggle=<n>` | `0.4` | Amplitude (pt) of the pencil/sketch decoration. Higher = more expressive. |
| `hidememo` | off | Suppress every `\memo{…}` so a final build carries no draft annotations. |

```latex
\usepackage[wiggle=1.2]{jambronotes}   % livelier pencil
\usepackage[hidememo]{jambronotes}      % clean final build
```

---

## Public API

### Typography
| Command | Usage |
| --- | --- |
| `\hand` | Font switch to the Augie handwriting font; use inside a group: `{\hand …}`. |
| `\handbold[<stroke>]{text}` | Bold handwriting; optional stroke width (default `0.6`). |
| `\firatitle` | Switch to the Fira Sans heading font (used by section headings). |

### Pencil marks
| Command | Usage |
| --- | --- |
| `\lapis[<color>]{text}` | Hand-drawn underline (default color `carandache` gold). |
| `\lapisbox[color=<c>,label=<id>]{text}` | Inline pencil box; `label=` makes a named TikZ node. |
| `\lapisboxeq[color=<c>]{math}` | Pencil box around display math (no `$`/`\[` needed). |
| `\jarrowup` `\jarrowdown` `\jarrowleft` `\jarrowright` | Inline sketched arrows (aliases `\jarrowr`, `\jarrowd`, …). Options: `color=`, `len=`, `lw=`, `pad=`. Follow with `\ ` to keep the trailing space. |
| `\marker{name}{text}` | Wrap visible text in a named node; a later `tikzpicture[tpstyle]` draws an `arrow` to it. |

### Notes & annotations
| Item | Usage |
| --- | --- |
| `notebox` (env) | Breakable shaded call-out for permanent content. |
| `\boxentry{title}` | First line of a `notebox` → numbered "Box X.Y", List-of-Boxes entry, and a `\label`-able anchor. |
| `\listofboxes` | Typeset the List of Boxes (after `\tableofcontents`). |
| `\NB{text}` | Inline draft "to-do" banner (transient; no suppression toggle). |
| `\memo[user=<u>,color=<c>]{text}` | Margin-style draft annotation. Defaults via `\memouser`, `\memocolor`; hidden by the `hidememo` option. |

### Structure, math, tables, code
| Item | Usage |
| --- | --- |
| `\para` | Auto-numbered paragraph; supports `\label`/`\ref`. |
| `\tocheading{text}` | Unnumbered heading in the TOC font (front matter). |
| `\backmatter` | Switch `\section` numbering to letters (A, B, …) for appendices. |
| `\E`, `\E_t` | Expectations operator shorthand. |
| `Y` column + `\notesize` | Centered auto-width `tabularx` column; `\notesize` (8.5 pt) for table notes. |
| `code` (env), `\mc{…}`, `\mci{…}` | Verbatim block and inline code chips on a light-gray background. |

> **Table-note tip:** when setting a multi-line note with `{\notesize …}`, put a
> `\par` inside the group (`{\notesize … text.\par}`) so the tight `\notesize`
> leading is applied to the paragraph rather than the surrounding body leading.

---

## Colors

Defined centrally and reusable anywhere a color is accepted:

`note` (reference blue), `title` (dark heading blue), `tomato`, `cobalt`,
`carandache` / `gold` (goldenrod), `codegray`, and the BoE palette
(`BoEblue`, `BoEacqua`, `BoEorange`, `BoEpurple`, `BoEgold`, `BoEgray`, `BoEred`).

---

## Changelog

See [`ChangeLog.md`](ChangeLog.md).
