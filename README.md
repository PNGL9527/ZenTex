<div align="center">

<img src="docs/images/banner.png" alt="ZenTex — a writing-first LaTeX workbench" width="720">

# ZenTex

**A writing-first LaTeX editor for macOS — native Swift shell, no Electron.**

The source stays the single source of truth. A decoration layer renders headings, math, tables, figures and citations *in place* — and you can keep typing on them.

![macOS](https://img.shields.io/badge/macOS-13%2B-black?logo=apple)
![Swift](https://img.shields.io/badge/shell-Swift%20%2F%20AppKit-F05138?logo=swift&logoColor=white)
![TypeScript](https://img.shields.io/badge/renderer-TypeScript%20%2B%20CodeMirror%206-3178C6?logo=typescript&logoColor=white)
![Size](https://img.shields.io/badge/app-~9%20MB-2E5A4C)
![Version](https://img.shields.io/badge/version-0.16.30-2E5A4C)

**English** · [简体中文](README.zh-CN.md)

</div>

<p align="center">
  <img src="docs/images/05-split-compiled.png" alt="ZenTex split view: decorated source on the left, compiled PDF on the right" width="100%">
  <br><sub>Split view — the same text on the left, the PDF <code>latexmk</code> actually compiled on the right. There is only ever one truth.</sub>
</p>

---

## Contents

- [Why ZenTex](#why-zentex)
- [Screenshot tour](#screenshot-tour)
- [Features](#features)
- [How it works](#how-it-works)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Development process](#development-process)
- [Verification](#verification)
- [Development commands](#development-commands)
- [Project structure](#project-structure)
- [Known limits](#known-limits)
- [Credits](#credits)
- [License](#license)

---

## Why ZenTex

Writing a paper in LaTeX means living between two views: plain source on one side, a compiled PDF on the other. Change one sentence and you hunt for it twice. Some editors fold the preamble and draw headings, but their preview is still a *separate rendering* — something you look at, not something you write in.

ZenTex starts from one question: **what if the rendered view *were* the source?**

It keeps the classic two-pane workflow (source left, compiled PDF right) and adds a **preview layer that decorates the very same buffer**: headings become real headings, formulas render with KaTeX, figures and tables appear inline, and `\label` / `\cite` / `\ref` fold into chips. Put the cursor on any decorated region and it turns back into editable source.

- **Native macOS app** (Swift + AppKit + WebKit). The whole bundle is ~9 MB; the Electron prototype it replaced was ~250 MB.
- **Self-contained.** The editing surface (CodeMirror 6 + pdf.js + KaTeX) is embedded in the app and never touches your file system directly.
- **Local only.** All file, compile and cross-reference operations go through a tiny HTTP service bound to `127.0.0.1` — it never listens on the network.
- **One process, no daemons.** The service lives inside the app and stops when you quit.

---

## Screenshot tour

> All screenshots are real captures of a live **v0.16.30** build (dark mode, 2× retina). The manuscript shown is a neutral demo project. The light theme uses the same design tokens and is switchable in Settings.

### 1 · Source mode — the familiar LaTeX, nothing hidden

<p align="center"><img src="docs/images/01-source.png" alt="Source mode with syntax highlighting, file tree and a narrow text column" width="100%"></p>

Syntax highlighting, a file tree (`figs/`, `sections/`, `main.tex`, `refs.bib`) and one narrow, readable text column.

### 2 · Preview mode — the same file, decorated

<p align="center"><img src="docs/images/02-preview.png" alt="Preview mode: preamble collapsed, headings rendered, citation chips" width="100%"></p>

The preamble collapses to a one-line summary bar, `\section` renders as a real heading (labelled with the command you actually wrote: `SEC`, never `PART`), and citations fold into chips.

### 3 · Math and tables rendered in place

| Formulas & enumerations | Tables |
| --- | --- |
| <img src="docs/images/03-preview-math.png" alt="Rendered KaTeX formulas and enumerated research questions"> | <img src="docs/images/04-preview-table.png" alt="A tabular rendered as a real table with booktabs rules"> |
| KaTeX math and lists rendered in the same column you type in. | A `tabular` rendered as a real table — booktabs rules and all. |

### 4 · Project tools

| Cross-file outline | TODO panel |
| --- | --- |
| <img src="docs/images/06-outline.png" alt="Cross-file outline panel"> | <img src="docs/images/07-todo.png" alt="TODO panel with checkboxes and progress"> |
| `\input` files are stitched in reading order; heading levels are normalised across the whole project. Click to jump to the right file and line. | Checking a box **really rewrites** the source (`% TODO` ↔ `% DONE`, `\todo{}` ↔ `\done{}`). |

| Project search | Citation completion |
| --- | --- |
| <img src="docs/images/08-search.png" alt="Project search with structural hits ranked first"> | <img src="docs/images/09-completion.png" alt="Citation completion popup inside \cite{"> |
| One box, two answers: structure (titles, labels) first, body text second — every hit carries `file:line:col`. | Type one letter inside `\cite{` and get candidates from every `.bib` and every cross-file `\label`, with author, year and venue. |

### 5 · Zen mode and settings

| Zen mode | Preferences |
| --- | --- |
| <img src="docs/images/11-zen-mode.png" alt="Zen mode: only the paragraph being edited stays in focus"> | <img src="docs/images/10-settings.png" alt="Preferences dialog with editor, typesetting and PDF reader groups"> |
| A focus mode that dims everything except the paragraph you are writing. | Three groups — editor assistants, typesetting, PDF reader. Persisted to `~/.zentex/settings.json`, every field validated on read. |

---

## Features

- **Hierarchical source editor.** A CodeMirror `StateField` decoration layer collapses the preamble, renders `\section`/`\subsection` as headings, renders inline and display math with KaTeX, shows `\includegraphics` and `tabular` inline, and folds `\label`/`\cite`/`\ref` into chips. Heading levels are **normalised per document**, so the same source gets sane levels under both `article` and `book`.
- **"Middle-state" WYSIWYG.** Preview mode is not a second render — it is the same source with decoration on top, so you type directly into it. Inline formatting works like a Markdown editor: `⌘B` / `⌘I` / `⌘U` wrap and **unwrap** `\textbf{}` / `\textit{}` / `\underline{}` idempotently, with multi-cursor and backward-selection support.
- **PDF preview with SyncTeX.** pdf.js renders the compiled PDF; jump source → PDF page, or click in the PDF to return to the source line. Nine page modes (including "two pages · cover"), thumbnail strip, zoom and scroll lock. Smart engine detection picks `pdflatex` / `xelatex` / `lualatex` from your document and project configuration.
- **Citations & completion.** `.bib` parsing, a project-wide label index, 15 built-in snippets with hotkeys (`<SELECTION>` wraps your selection), environment templates and smart selection. Reference numbers are deliberately never "predicted" — LaTeX numbering is a compile-time decision.
- **Project tools.** File tree, cross-file outline, TODO tracking, project statistics, label search, full-text project search, find & replace, spell checking with a user dictionary, and automatic typesetting after a typing pause.
- **Dark mode + custom CSS.** Editor, preview and shell share one set of design tokens (`src/theme/tokens.css`, `--ltx-*`). Light / dark / follow-system only flips `<html data-theme>`. Paste arbitrary CSS in **Settings → Appearance**; it is injected last and overrides everything.
- **Chinese out of the box.** `ctex` templates compile under XeLaTeX/LuaLaTeX, plus a Chinese punctuation panel and a manual TeX-distribution path. The interface itself is available in English and Chinese.
- **Safe by construction.** The renderer never reads or writes files; the Swift backend rejects any path escaping the project root and listens only on loopback.

---

## How it works

Three layers, each with one job:

```
ZenTex.app
├── Swift shell (AppKit + WebKit)
│   ├── NSWindow + WKWebView            renders the UI
│   ├── NWListener (Network.framework)  local HTTP service, 127.0.0.1 only
│   ├── Process                         invokes latexmk / synctex
│   └── native menu, NSOpenPanel, folder drag-and-drop
├── Render layer (TypeScript + CodeMirror 6 + pdf.js + KaTeX)
│   └── embedded in Contents/Resources/web, never touches the filesystem
└── Local TeX distribution
    └── latexmk → PDF + .synctex.gz
```

**The middle state — three rules.** The preview is a single CodeMirror `StateField` drawing decorations over the same buffer:

1. **The cursor restores.** Move into a decorated region and it becomes editable source again.
2. **Nested regions don't stack.** The outer region wins — no matryoshka of widgets.
3. **One scan per document state.** Parsing is cached, so typing never triggers a full re-scan.

**Why "native shell + WebView" instead of pure native controls?** The heart of the experience is an *editable decoration layer on top of text* — headings, math, figures, tables, chips. CodeMirror's `StateField` / `Decoration` model is the mature answer to exactly that; rebuilding it in `NSTextView` would cost more and achieve less. So the *shell* (window, menus, file dialogs, process management) is native Swift, while the *editing experience* uses web technology.

**The backend is Swift too.** The original Node reference host (`host/*.mjs`) was ported file by file into `swift/Sources/ZenTex/` — HTTP service, path guarding, file tree, main-file detection, `latexmk` invocation, `.log` parsing and bidirectional SyncTeX. The HTTP contract is **field-for-field identical**, so the frontend did not change a line. `host/*.mjs` stays as the reference implementation and as the dev backend for `npm run dev`.

---

## Requirements

- **macOS 13 Ventura or later.**
- **A TeX distribution** for the standard build — TeX Live 2026 and MacTeX are verified (`latexmk`, `pdflatex` and `synctex` must be installed).
  - *Experimental:* the `appstore` branch bundles a minimal TeX distribution inside the app (~380 MB), so it runs on a Mac with **no system TeX** — the basis for exploring Mac App Store distribution. It is kept on its own branch; mainline features are ported over as needed.
- To **build from source**: **Node.js 20+** and **Xcode Command Line Tools** (or Xcode).

---

## Installation

### Download a prebuilt disk image

Grab the latest `ZenTex 0.16.30.dmg` from the [Releases](../../releases) page, double-click to mount, and drag **ZenTex** into **Applications**.

> The build is ad-hoc signed, not notarised, so macOS may say "unidentified developer" on first launch. Right-click → **Open**, or run:
> ```bash
> xattr -dr com.apple.quarantine "/Applications/ZenTex.app"
> ```

### Build from source

```bash
npm install
npm run app          # build frontend → compile Swift → assemble & launch ZenTex.app
```

The resulting `ZenTex.app` is self-contained (~9 MB). To produce a distributable disk image:

```bash
npm run dmg          # package the existing .app into ZenTex 0.16.30.dmg
npm run dmg:fresh    # reassemble the .app, then package
```

---

## Usage

1. **First launch** asks you to pick a LaTeX project folder; afterwards the app returns to your last project. Use `⌘O` to switch.
2. **Write** in the left pane. Toggle **Source / Preview** in the toolbar to open or close the decoration layer.
3. **Compile** with the toolbar button (or the typeset shortcut). The PDF appears on the right; errors show in the problems panel with file and line.
4. **Jump** between source and PDF with the locate buttons or by clicking in the PDF (SyncTeX).
5. **Settings** (`⌘,`) cover appearance, engine, spelling and a free-form CSS box.

| Shortcut | Action |
| --- | --- |
| `⌘O` | Open / switch project |
| `⌘,` | Settings |
| `⌘B` / `⌘I` / `⌘U` | Wrap / unwrap bold / italic / underline |
| `⌘F` · `⌘G` / `⇧⌘G` | Find · next / previous match |
| `⌘E` | Use selection for find |
| `Ctrl-<letter>` | Insert a built-in snippet (base key switchable to `Ctrl+Shift`) |

---

## Development process

ZenTex was built solo, end to end — design, editor architecture, Swift shell and backend, packaging pipeline and verification suite. It grew out of the author's own academic writing: a manuscript with cross-file includes, citations and tables, where every existing editor forced a choice between clean source and a readable preview.

### The story in five steps

1. **Define the problem.** Two views (source / PDF) mean hunting for every edit twice, and a "preview" that is a separate rendering is something you look at, not write in.
2. **Study a reference first.** An existing macOS LaTeX editor (Texifier 1.9.33) was mapped completely before writing code — all **1025** string-table keys, the NIB menu tree and the real `NSUserDefaults` key names. ZenTex was then re-implemented from scratch on the same LaTeX toolchain (`latexmk`, SyncTeX, TeX Live), **without modifying the reference app's binary or redistributing its assets**. The inventory and item-by-item parity matrix live in `docs/`.
3. **Prototype on the web stack.** A first prototype used Electron plus a Node reference host (~250 MB).
4. **Go native.** The shell moved to Swift/AppKit and the backend was ported file by file to Swift, keeping the HTTP contract identical — the app dropped to ~9 MB with no Electron and no Node runtime.
5. **Polish and prove.** Long iteration on writing ergonomics, then a six-layer verification pipeline (below) that ends by running the self-test *from inside the finished, read-only disk image*.

### Version timeline

The full per-version log is in [CHANGELOG.md](CHANGELOG.md). Milestones:

| Version | Milestone |
| --- | --- |
| 0.4 | Window dragging, menu bar and icon tab fixes |
| 0.5 | Simplified UI; follows the macOS system accent colour |
| 0.6 | Typeset-complete notifications |
| 0.7 | Adjustable editing column width and **focus mode** |
| 0.8 | Settings regrouped into categories; menus merged |
| 0.9 | Completion popup: new material and icons |
| 0.10 | **Renamed to ZenTex**; three-layer icon-cache handling |
| 0.11 – 0.12 | Auto-closing braces; Enter continues list items in preview; completion re-triggers after accepting |
| 0.13 – 0.14 | Narrow-column line numbers; PDF zoom keeps aspect ratio; auto-load the same-named PDF; focus mode decoupled from dark mode |
| 0.16.6 | **Chinese writing**: one template and one pre-flight check |
| 0.16.9 – 0.16.13 | PDF selection, links, pinch zoom; user dictionary for spelling; smart selection; editor context menu |
| 0.16.16 – 0.16.22 | Smart engine detection learns project build config and LuaLaTeX-only signals |
| 0.16.23 | Manual TeX-distribution path + Chinese punctuation panel |
| 0.16.24 – 0.16.30 | Two-way TODO jumping (gutter marker ↔ sidebar card), toggleable TODO line tracking, instant label indexing, many preview-mode stability fixes |

### Packaging pipeline

`npm run dmg` assembles the `.app`, writes the Finder layout `.DS_Store` **directly in pure Python** (Finder automation is blocked on the build machine), builds a compressed `UDZO` image with `hdiutil`, verifies the ad-hoc signature, then mounts the finished image and runs the app self-test from the read-only volume.

### Notes for maintainers

- `scripts/build-mac.mjs` deliberately **bypasses `xcrun`** and calls `swiftc` by absolute path. If the machine never accepted the Xcode licence, `xcrun` hangs on a licence prompt in non-interactive environments; the absolute-path call works fine.
- The build performs an **ad-hoc signature** (`codesign --sign -`). On Apple Silicon this is not optional — editing `Info.plist` invalidates the original signature, and an unsigned arm64 app is rejected by the kernel. If the toolchain complains, `sudo xcodebuild -license accept` fixes it at the root.
- The icon is `assets/icon.png` (a retro typewriter on stationery, 1024×1024, no text). `scripts/icon-master.py` de-whitens / crops / feathers the master; `build-mac.mjs` turns it into `app.icns` via `sips` + `iconutil`.

---

## Verification

Six independent layers. Numbers correspond to **v0.16.30**.

| Command | Coverage | Result |
| --- | --- | --- |
| `npm run typecheck` | `tsc --noEmit` | 0 errors |
| `npm test` | Frontend pure functions: LaTeX scanner, `.bib` parsing, label index, log parsing, wrap/unwrap decision matrix, caption flattening, project search, TODO source rewrite, autotypeset state machine, page-mode arithmetic, snippet expansion, outline / TODO / stats, settings validation… | **667** passing |
| `npm run selftest` | Swift backend: a **real HTTP server and real requests** across all **39** endpoints, path-escape guarding (`../../etc/passwd` must be `403`), PDF compile, SyncTeX round-trip, menu structure, drag-mask hit testing, notification decision table, ChkTeX lint | **237** checks |
| `npm run check:templates` | The six built-in templates each really compiled by `latexmk` (`ctexart` via XeLaTeX) | 6 / 6 |
| `npm run smoke` | Production-build, real-browser end-to-end (real Chrome, real keystrokes, real compile) | **871** checks |
| `npm run dmg` | Mount the finished image, `codesign --verify`, then run the app self-test **from the read-only image** | 10 / 10 |

Neither `selftest` nor `smoke` touches *your* preferences: settings live outside the browser profile (`~/.zentex/settings.json`), tests point `REA_CONFIG` at a temp directory, and the smoke suite snapshots, force-kills the browser and restores byte-for-byte afterwards.

---

## Development commands

| Command | What it does |
| --- | --- |
| `npm run app` | Build frontend, compile Swift, assemble and launch the app |
| `npm run build:mac` | Build only, do not launch |
| `npm run dmg` / `npm run dmg:fresh` | Package the current `.app` / reassemble first, then package |
| `npm run icon` | Regenerate the app icon |
| `npm run refresh-icon` | Re-register and clear the icon cache (Dock shows a stale icon) |
| `npm run selftest` | Headless self-test of the Swift backend |
| `npm run typecheck` | `tsc --noEmit` |
| `npm test` | Frontend unit tests |
| `npm run smoke` | Real-browser end-to-end verification (needs Chrome) |
| `npm run dev` | Frontend dev mode: Vite + Node reference backend, hot reload |

---

## Project structure

```
swift/Sources/ZenTex/          Swift shell + backend
  main.swift                   entry; --selftest runs headless
  AppDelegate.swift            window, menu, NSOpenPanel, drag-drop
  HTTP.swift                   minimal HTTP/1.1 over Network.framework (loopback only)
  Router.swift                 routes; contract matches the Node host field-for-field
  Project.swift                file tree, path guarding, main-file detection
  LaTeX.swift                  latexmk invocation + SyncTeX round-trip
  LogParser.swift              .log → structured diagnostics
  ProcessRunner.swift          child process + hard timeout + output capture
  Toolchain.swift              locate TeX Live, fix PATH for GUI apps
  AppState.swift               project root, settings, custom CSS
  StaticFiles.swift            serve UI assets
  SelfTest.swift               backend self-test
scripts/
  build-mac.mjs                compile Swift → assemble .app → embed dist → ad-hoc sign
  build-dmg.mjs                .app → writable image → Finder layout → UDZO → verify
  dmg-layout.py                write .DS_Store directly (Finder automation blocked)
  selftest.mjs                 recompile if needed, then run backend self-test
  refresh-icon.mjs             refresh icon cache; report unreachable layer
  icon-master.py               master de-whiten / crop / feather
  smoke.mjs                    real-browser end-to-end verification
  dev.mjs                      frontend dev mode: Node host + Vite
host/                          Node reference implementation (kept behaviour-identical)
src/editor/                    LaTeX scanner, tokenizer, decorations, widgets, completion
src/app/                       settings model, column layout, sidebar, autotypeset, api
src/preview/                   pdf.js render, page modes, problems
src/theme/tokens.css           design tokens — the shared source of truth
tests/                         unit tests
docs/                          screenshots + design notes
project/                       sample LaTeX project
```

---

## Known limits

Honest edges, documented rather than hidden:

- No continuous scroll-following while typing (SyncTeX jumps work both ways on demand).
- Inline-format shortcuts cover six style families; other commands render but have no shortcut family.
- `ctex` templates require XeLaTeX or LuaLaTeX.
- On a machine whose Xcode licence has not been accepted, `biber` can fail (`lipo`); biblatex projects then fall back to the BibTeX chain.
- The interface is bilingual (English / Chinese) with **one known leak**: the collapsed preamble summary bar in preview mode is hard-coded Chinese (visible in the screenshots).
- The standard build is ad-hoc signed, not notarised.

---

## Credits

- Independent reimplementation: no third-party app binary is modified or redistributed — only the same LaTeX toolchain (`latexmk`, SyncTeX, TeX Live) is reused.
- Built on [CodeMirror 6](https://codemirror.net/), [pdf.js](https://mozilla.github.io/pdf.js/) and [KaTeX](https://katex.org/), under their respective open-source licences.
- App icon (the typewriter), window and interface design: the author.
- Screenshots use a neutral demo manuscript (a postphenomenological account of technological mediation in human–vehicle co-driving) — no private content.

ZenTex is inspired by the dedicated-source / PDF side-by-side workflow serious LaTeX writers rely on, and by the many small editing conveniences that make long documents bearable to write.

---

## License

No license file is present yet. **Add a `LICENSE` file before publishing** (MIT or Apache-2.0 are common choices). Until then, all rights are reserved.
