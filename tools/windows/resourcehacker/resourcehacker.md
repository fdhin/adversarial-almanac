---
name: "resourcehacker"
categories:
  - blackarch-windows
version: "5.1.8"
homepage: "http://www.angusj.com/resourcehacker/"
packaged-by: [blackarch]
blackarch-builddate: "2021-06-23"
build-system: windows
meta-checked: "2026-11-09"
github-repo: "Seabreg/resource_hacker"
github-stars: 88
github-pushed: "2019-03-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# resourcehacker

> Freeware resource editor for Windows PE files: view, extract, replace and compile icons, dialogs, strings and other resources.

## What it's for

Resource Hacker (by Angus Johnson, angusj.com) is the classic Windows
resource compiler/decompiler. It opens 32- and 64-bit PE executables
(.exe, .dll, .scr, ...) and both compiled (.res) and source (.rc)
resource files, and lets you view, add, rename, modify, extract or delete
the resources inside: icons, bitmaps, cursors, dialogs, menus, string
tables, version info, manifests and more. Nearly everything the GUI does
is also scriptable from the command line with `-open/-save/-action/
-resource/-mask/-log` switches, which makes it a standard tool for
batch-patching binaries — swapping an icon, editing a version block or
ripping icons and dialogs out of a program. It is closed-source freeware
distributed only from the author's website (the site always serves the
current build); BlackArch packages the Windows executable under
`/usr/share/windows/resourcehacker/`.

GitHub: third-party mirror of the angusj.com freeware distribution (older release snapshot).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Extract every icon from a binary

Pull all icon groups out of an executable as individual icon files.

```
ResourceHacker.exe -open source.exe -save icons -action extract -mask ICONGROUP,, -log CON
```

Outcome: writes one `.ico` file per icon group into the `icons` directory
and logs each extraction to the console; `-mask ICONGROUP,,` selects all
icon groups with any name.

### Replace an application's icon

Swap the main icon of a program without touching its code.

```
ResourceHacker.exe -open target.exe -save target_patched.exe -action addskip -res MAINICON.ico -mask ICONGROUP,MAINICON,
```

Outcome: writes `target_patched.exe` with `MAINICON.ico` as the
`MAINICON` icon group; `addskip` keeps the file unchanged if a resource
with that name already exists — use `addoverwrite` to replace it.

### Dump dialogs to an editable resource script

Export a binary's dialogs for inspection or retranslation.

```
ResourceHacker.exe -open target.exe -save dialogs.rc -action extract -mask DIALOG,, -log rh.log
```

Outcome: writes `dialogs.rc`, a text resource script containing every
dialog template, plus a log of the run — open it in any editor to read
control layouts or prepare modifications.

### Compile a resource script into a .res file

Turn an edited `.rc` script into a compiled resource file for linkers or
later injection.

```
ResourceHacker.exe -open resources.rc -save resources.res -action compile -log NUL
```

Outcome: produces `resources.res`, the compiled form of the script, ready
to be linked into a build or added to another PE with `-action add`.
