---
name: "leo"
categories:
  - blackarch-misc
version: "39941.f094f5b9d9"
homepage: "https://github.com/leo-editor/leo-editor/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "leo-editor/leo-editor"
github-stars: 1614
github-pushed: "2026-11-07"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3337
gh-activity-checked: "2026-11-09"
---

# leo

> Outline-oriented editor and literate-programming workbench for organizing code, notes and projects.

## What it's for

Leo (leo-editor) is a programmer's editor and project manager built around hierarchical outlines: every document is a tree of nodes, each with a headline and a body of text or code. Nodes can hold sections of external files (literate programming with noweb-style named blocks and sentinel comments), so a codebase can be organized in whatever order helps the human while Leo still writes and reads plain files on disk. It is scriptable end-to-end in Python — commands, plugins and batch scripts share one API — which makes it usable as a personal wiki, a code-reading tool (clone nodes gather scattered definitions into one view), or a headless outline processor. It is general-purpose editor software, not a security tool; its presence in security distributions is a packaging artifact.

## Typical use cases

### Open an outline in the GUI

Start Leo on an outline file.

```
leo notes.leo
```

Outcome: the Qt outline editor opens `notes.leo`; on first run Leo asks for an identifier and stores it in `~/.leo/.leoID.txt`. With no file argument it opens the default workbook outline.

### Run Leo without a desktop

Use the console interface when only a terminal is available.

```
leo --gui=console notes.leo
```

Outcome: Leo starts in console mode on the given outline (the installed `leo-console` launcher does the same), editing headlines and bodies entirely from the keyboard; `--gui` also accepts `curses`, `qt`, `text` and `null`.

### Batch-process an outline headlessly

Execute a Python script against outlines without opening any windows.

```
leo --script=headlines.py notes.leo
```

Outcome: Leo enters batch mode — no GUI — runs `headlines.py` with Leo's full scripting API available (e.g. iterating `c.all_positions()` to print every headline), prints script output to the console and exits; `--silent` suppresses log messages.

*No corpus evidence — derived from upstream documentation.*
