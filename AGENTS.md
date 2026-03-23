# AI Agent Instructions — JambroNotes Style Maintenance

## 0) Mission (non-negotiable deliverables)

Your job is to maintain a small, disciplined "notes style repo" with three always-up-to-date artifacts:

1. **`JambroNotes.sty`**

   * Clean, logically ordered, well commented.
   * Compiles without errors/warnings *caused by the style* (document-specific warnings are acceptable, but flag them).
   * Backwards compatible unless explicitly instructed otherwise.

2. **`ChangeLog.md`**

   * Lists **all changes since the previous Git commit** (not "since last time you looked").
   * Clear, concrete, and searchable.

3. **`template.tex`**

   * A minimal but complete starter document that **exercises every user-facing feature** of the `.sty`.
   * Compiles cleanly and demonstrates the intended look.

**Definition of done:** all three compile / render correctly together, and the changelog matches the diff against the previous commit.

---

## 1) Working principles

### 1.1 Don't bluff

* **Never claim something works unless you compiled `template.tex` successfully.**
* If you cannot test (e.g., missing TeX packages), say so explicitly and provide the best safe alternative.

### 1.2 Minimal, safe edits

* Prefer **small, local changes**.
* Avoid reformatting unrelated sections.
* No "drive-by" style changes unless they fix a real problem.

### 1.3 Backwards compatibility

* If a change could break existing documents:

  * Provide a compatibility shim where feasible, or
  * Gate it behind an option toggle, or
  * Very clearly document it as **BREAKING** in `ChangeLog.md`.

### 1.4 Commenting standard

* Comments should explain **why** (intent, constraints, tradeoffs), not narrate **what** LaTeX already says.
* Use short section headers and consistent comment style.

---

## 2) Repository workflow (always follow)

### Step A — Start from Git truth

* Identify the **previous Git commit** as the baseline.
* Your changelog must correspond to `git diff <previous-commit>..HEAD` (or staged diff if preparing a commit).

### Step B — Make the change

* Update `JambroNotes.sty`.
* Update/extend `template.tex` so the modified feature is exercised.
* Update `ChangeLog.md` with an entry that matches the actual diff.

### Step C — Test

Run at least:

* `latexmk -pdf -interaction=nonstopmode template.tex`
  (or an equivalent compile command)

Record outcomes:

* If errors: fix and recompile.
* If warnings: decide if style-caused; if yes, fix or document.

### Step D — Consistency check

* Ensure every new/changed user-facing command, color, environment, or option:

  * is documented in comments in `.sty`,
  * appears in `template.tex`,
  * is described in `ChangeLog.md`.

---

## 3) `JambroNotes.sty` structure rules

### 3.1 Ordering (keep stable)

Organize the file into predictable blocks (use clear section headers):

1. **Preamble / guards**

   * `\NeedsTeXFormat`, `\ProvidesPackage`, version string.
2. **Packages**

   * All `\usepackage` calls, grouped logically (encoding, math, layout, fonts, graphics, utilities).
3. **Colors**

   * Centralize all `\definecolor` definitions in one place.
4. **Environments**

   * tcolorbox environments and other custom environments.
5. **Format / layout settings**

   * `\parindent`, `\parskip`, list spacing, etc.
6. **Commands — general**

   * Utility macros (`\NB`, `\E`, `\ACB`, etc.).
7. **TikZ / drawing**

   * Library loads, decoration definitions, style defs.
8. **Pencil commands (public API)**

   * `\lapis`, `\lapisbox`, `\lapisboxeq`.
9. **Tables**

   * Column types, `\arraystretch`, custom sizes.
10. **Section & heading formatting**
11. **Paragraph numbering**

Do not scatter related settings across the file.

### 3.2 Public API discipline

* Treat any command/environment intended for document authors as **public API**.
* For each public API item:

  * Provide a one-line doc comment with usage syntax.
  * Add an example in `template.tex`.

### 3.3 Naming conventions

* Internal helpers: prefix with `jambro@` (e.g., `\jambro@color`).
* Public commands: readable, consistent naming; avoid cryptic abbreviations.

### 3.4 Avoid fragile hacks

* Prefer standard LaTeX mechanisms over low-level patching.
* If patching is necessary, comment:

  * what is being patched,
  * why it is necessary,
  * what could break.

---

## 4) `template.tex` requirements

`template.tex` is not a real notes document; it is a **feature showcase**.
**Rule:** If it is in `.sty` and user-facing, it must appear in `template.tex`.

Cover at minimum:

* Title block with all fields populated
* `\section` and `\subsection` (Roman/Alph numbering)
* `\para` paragraph numbering
* `notebox` environment
* `\NB{...}` todo note
* `\lapis{...}` pencil underline and `\lapis[color]{...}` variant
* `\lapisbox{...}` and `\lapisbox[color=...,label=...]{...}`
* `\lapisboxeq{...}` in a display equation
* `\hand` / `\handbold` font
* `\ACB{...}` annotation
* A table using `tabularx` with `Y` column type
* A `tikzpicture` with a pencil-style arrow

---

## 5) `ChangeLog.md` rules (must match Git diff)

### 5.1 Format (simple and consistent)

Use one entry per "work session" (or per commit). Template:

* **Version X.Y — YYYY-MM-DD** — short title

  * **Added:** …
  * **Changed:** …
  * **Fixed:** …
  * **Deprecated:** …
  * **Removed:** …
  * **BREAKING:** …

### 5.2 Content rules

* Be concrete: reference command names and features.
* If behavior changed: describe *before → after*.
* If a change required updating `template.tex`, mention it.
* If you fixed a bug: include the symptom and the cause (briefly).

---

## 6) Quality gates before you say "done"

You may only mark work complete if all are true:

* `template.tex` compiles successfully.
* The style compiles without introducing errors.
* `template.tex` demonstrates every public feature.
* `ChangeLog.md` accurately reflects the actual diff vs previous commit.
* Comments in `.sty` are updated where behavior changed.

---

## 7) What to do when something is unclear

* Don't guess user intent.
* Implement the safest default (backwards compatible).
* Document assumptions in comments + changelog.
* If a design choice is ambiguous, provide **two options**:

  * default (safe),
  * alternative (behind an option toggle or conditional), with clear instructions.

---

## 8) Communication style in outputs

* Dry, precise, minimal fluff.
* When reporting changes, prefer:

  * "I changed X to Y because Z; template shows it in section N."
* If something is unverified, state it plainly.
