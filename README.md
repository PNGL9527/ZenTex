<div align="center">

<img src="docs/images/banner.png" alt="ZenTex — a writing-first LaTeX workbench" width="720">

# ZenTex

**A writing-first LaTeX editor for macOS — native, light, and built for long documents.**

The source stays the single source of truth. Headings, math, tables, figures and citations are rendered *in place* — and you can keep typing on them.

![macOS](https://img.shields.io/badge/macOS-13%2B-black?logo=apple)
![Native](https://img.shields.io/badge/native-Swift%20%2F%20AppKit-F05138?logo=swift&logoColor=white)
![Size](https://img.shields.io/badge/app-~9%20MB-2E5A4C)
![Version](https://img.shields.io/badge/version-0.16.30-2E5A4C)
![Mac App Store](https://img.shields.io/badge/Mac%20App%20Store-coming%20soon-lightgrey?logo=appstore&logoColor=white)

**English** · [简体中文](README.zh-CN.md)

</div>

<p align="center">
  <img src="docs/images/05-split-compiled.png" alt="ZenTex split view: decorated source on the left, compiled PDF on the right" width="100%">
  <br><sub>Split view — your text on the left, the real compiled PDF on the right. There is only ever one truth.</sub>
</p>

---

## Contents

- [Why ZenTex](#why-zentex)
- [Screenshot tour](#screenshot-tour)
- [Features](#features)
- [Privacy and design](#privacy-and-design)
- [Requirements](#requirements)
- [Getting ZenTex](#getting-zentex)
- [Quick start](#quick-start)
- [How it was built](#how-it-was-built)
- [Known limits](#known-limits)
- [Feedback](#feedback)
- [Credits](#credits)
- [License](#license)

---

## Why ZenTex

Writing a paper in LaTeX means living between two views: plain source on one side, a compiled PDF on the other. Change one sentence and you hunt for it twice. Some editors fold the preamble and draw headings, but their preview is still a *separate rendering* — something you look at, not something you write in.

ZenTex starts from one question: **what if the rendered view *were* the source?**

It keeps the classic two-pane workflow and adds a **preview layer that decorates the very same text**: headings become real headings, formulas render with KaTeX, figures and tables appear inline, and `\label` / `\cite` / `\ref` fold into small chips. Put the cursor on any decorated region and it turns back into editable source.

- **A native Mac app.** About 9 MB, no Electron.
- **Self-contained.** Everything you need to write is inside the app.
- **Local and private.** Your files stay on your Mac.

---

## Screenshot tour

> All screenshots are real captures of a live **v0.16.30** build (dark mode). The manuscript shown is a neutral demo project. A light theme is available in Settings.

### 1 · Source mode — the familiar LaTeX, nothing hidden

<p align="center"><img src="docs/images/01-source.png" alt="Source mode with syntax highlighting, file tree and a narrow text column" width="100%"></p>

Syntax highlighting, a file tree and one narrow, readable text column.

### 2 · Preview mode — the same file, decorated

<p align="center"><img src="docs/images/02-preview.png" alt="Preview mode: preamble collapsed, headings rendered, citation chips" width="100%"></p>

The preamble collapses to a one-line bar, `\section` renders as a real heading (labelled with the command you actually wrote), and citations fold into chips.

### 3 · Math and tables rendered in place

| Formulas & lists | Tables |
| --- | --- |
| <img src="docs/images/03-preview-math.png" alt="Rendered KaTeX formulas and enumerated research questions"> | <img src="docs/images/04-preview-table.png" alt="A tabular rendered as a real table with booktabs rules"> |
| KaTeX math and lists, in the same column you type in. | A `tabular` as a real table — booktabs rules and all. |

### 4 · Tools for long documents

| Cross-file outline | TODO panel |
| --- | --- |
| <img src="docs/images/06-outline.png" alt="Cross-file outline panel"> | <img src="docs/images/07-todo.png" alt="TODO panel with checkboxes and progress"> |
| Included files are stitched in reading order, with heading levels normalised across the whole project. | Checking a box **really rewrites** your `% TODO` / `\todo{}` comment in the source. |

| Project search | Citation completion |
| --- | --- |
| <img src="docs/images/08-search.png" alt="Project search with structural hits ranked first"> | <img src="docs/images/09-completion.png" alt="Citation completion popup inside \cite{"> |
| One box, two answers: structure (titles, labels) first, body text second — every hit has file, line and column. | Type one letter inside `\cite{` and get candidates from every `.bib` and cross-file `\label`, with author, year and venue. |

### 5 · Zen mode and settings

| Zen mode | Preferences |
| --- | --- |
| <img src="docs/images/11-zen-mode.png" alt="Zen mode: only the paragraph being edited stays in focus"> | <img src="docs/images/10-settings.png" alt="Preferences dialog with editor, typesetting and PDF reader groups"> |
| Everything fades except the paragraph you are writing. | Editor assistants, typesetting and PDF reader — each in its own group. |

---

## Features

- **Middle-state editing.** Preview mode is not a second render: it is your source with decoration on top, so you type directly into it. `⌘B` / `⌘I` / `⌘U` wrap and unwrap bold, italic and underline like a Markdown editor.
- **Headings that make sense.** `\section`, `\subsection` and friends render as real headings, with levels normalised per document so `article` and `book` both read naturally.
- **Math, tables, figures.** KaTeX math, `tabular` tables and `\includegraphics` figures appear inline.
- **PDF preview with SyncTeX.** Jump from source to PDF, or click the PDF to return to the source line. Nine page modes, thumbnail strip, zoom and scroll lock. The engine (`pdflatex` / `xelatex` / `lualatex`) is detected automatically.
- **Citations and completion.** `.bib` parsing, a project-wide label index, 15 built-in snippets with hotkeys, environment templates and smart selection.
- **Project tools.** File tree, cross-file outline, TODO tracking, statistics, label and full-text project search, find & replace, spell checking with a personal dictionary, and automatic typesetting after you pause typing.
- **Dark mode and custom CSS.** Light, dark or follow-system, plus a free-form CSS box in **Settings → Appearance** for your own look.
- **Chinese out of the box.** `ctex` templates for XeLaTeX / LuaLaTeX, a Chinese punctuation panel, and an interface in English and Chinese.
- **Zen mode.** A focus mode that fades everything but the paragraph you are writing.

| Shortcut | Action |
| --- | --- |
| `⌘O` | Open / switch project |
| `⌘,` | Settings |
| `⌘B` / `⌘I` / `⌘U` | Bold / italic / underline (press again to unwrap) |
| `⌘F` · `⌘G` / `⇧⌘G` | Find · next / previous match |
| `⌘E` | Use selection for find |
| `Ctrl-<letter>` | Insert a built-in snippet |

---

## Privacy and design

- **Your documents stay on your Mac.** ZenTex runs as a single process. Its internal services listen only on the local loopback address and stop when you quit — nothing is exposed to the network, and there are no background daemons.
- **Contained by design.** The editing surface never touches your files directly; file access is restricted to the project folder you open.
- **Settings are plain and local**, stored in `~/.zentex/settings.json`.

---

## Requirements

- **macOS 13 Ventura or later.**
- **A TeX distribution** — TeX Live 2026 and MacTeX are verified (`latexmk`, `pdflatex` and `synctex` must be installed). An edition with TeX built in, which needs no separate install, is in preparation for the Mac App Store.

---

## Getting ZenTex

**Mac App Store:** coming soon.

---

## How it was built

ZenTex was designed and built by one person, end to end. It grew out of the author's own academic writing — a manuscript with cross-file includes, citations and tables — where every existing editor forced a choice between clean source and a readable preview.

1. **Define the problem.** Two views mean hunting for every edit twice, and a "preview" that is a separate rendering can be looked at but not written in.
2. **Study before building.** An existing macOS LaTeX editor was surveyed in detail, and ZenTex was then designed from scratch on the same standard LaTeX toolchain.
3. **Prototype.** A first web-based prototype (~250 MB) proved the "middle-state" editing idea.
4. **Go native.** The shell moved to Swift and AppKit — the app dropped to about 9 MB.
5. **Polish and prove.** Long iteration on writing ergonomics, backed by layered automated testing: **667** unit tests, a **237**-check backend self-test, **871** real-browser end-to-end checks, and compile tests of all six built-in templates.

**Milestones** (full history in [CHANGELOG.md](CHANGELOG.md)):

| Version | Milestone |
| --- | --- |
| 0.5 | Interface simplified; follows the macOS system accent colour |
| 0.7 | Adjustable editing column width and focus mode |
| 0.9 | Redesigned completion popup |
| 0.10 | Renamed **ZenTex** |
| 0.12 – 0.14 | Smoother preview editing; PDF zoom and loading improvements |
| 0.16.x | Chinese writing support, spelling dictionary, smart selection, smarter engine detection, two-way TODO jumping |

---

## Known limits

- No continuous scroll-following while typing (source ↔ PDF jumps work both ways on demand).
- Inline-format shortcuts cover six style families; other commands render but have no shortcut.
- `ctex` templates require XeLaTeX or LuaLaTeX.
- The interface is bilingual (English / Chinese) with **one known leak**: the collapsed preamble bar in preview mode is currently shown in Chinese.

---

## Credits

- Built on [CodeMirror 6](https://codemirror.net/), [pdf.js](https://mozilla.github.io/pdf.js/) and [KaTeX](https://katex.org/), used under their respective open-source licences. Typesetting is done by your local TeX distribution.
- App icon, window and interface design: the author.
- Screenshots use a neutral demo manuscript — no private content.

---

## License

ZenTex is proprietary software. © 2026 — all rights reserved. This repository hosts documentation, release notes and issue tracking only; it does not contain the application's source code.
