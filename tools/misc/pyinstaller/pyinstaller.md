---
name: "pyinstaller"
categories:
  - blackarch-misc
version: "6.22.3"
homepage: "https://pyinstaller.org"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-13"
build-system: python
license: Apache-2.0, GPL-2.0-or-later, MIT
meta-checked: "2026-11-09"
github-repo: "pyinstaller/pyinstaller"
github-stars: 13142
github-pushed: "2026-11-06"
github-archived: false
github-issues: 117
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 321
gh-activity-checked: "2026-11-09"
---

# pyinstaller

> Freezes a Python program together with the interpreter into a standalone executable.

## What it's for

PyInstaller analyzes a Python script, discovers every module and library it needs, and bundles the script, those dependencies, and the active Python interpreter into a single folder or a single executable file that runs without Python installed. It works on Windows, macOS, and Linux but is not a cross-compiler: a bundle only runs on the OS and architecture it was built on. In offensive work it is the standard way to turn a Python implant, dropper, or agent script into a portable binary; on the defensive side, executables it produces are recognizable (bootloader strings, an appended archive) and the original bytecode can be recovered with tools such as pyinstxtractor.

## Typical use cases

### Package a script as a single self-contained executable

Turn a Python script into one file that runs on machines without Python installed.

```
pyinstaller -F --name "tool" payload.py --specpath /tmp
```

Outcome: writes the build spec into the `--specpath` directory and produces a single executable named `tool` under `dist/`, plus a `build/` directory of intermediates. The file runs standalone on the same OS and architecture it was built on.

### Build from a spec file

Rebuild an executable from the generated `.spec` file, which records every build option and can be edited by hand.

```
pyinstaller tool.spec
```

Outcome: PyInstaller constructs the executable exactly as the spec's `Analysis(...)`, `PYZ(...)` and `EXE(...)` entries describe — options such as `datas=`, `hiddenimports=` and `excludes=` apply even when the equivalent command-line flags are never passed.

### Bundle a data file into the executable

Ship a resource file (config, wordlist, readme) inside the bundle and locate it at runtime.

```
pyinstaller --add-data 'src/README.txt:.' tool.py
```

Outcome: the file is placed at the top level of the bundle — alongside the executable in one-folder mode, or inside the archive in one-file mode, where the bootloader extracts it to the `sys._MEIPASS` directory at startup. On Windows the separator is `;` instead of `:`.

### Copy a readable file out of a privileged build context via spec `datas`

When a wrapper script or scheduled task runs `pyinstaller` on a user-supplied `.spec` file as a more privileged user, edit the spec's `Analysis` entry so `datas` names any file that user can read.

```
pyinstaller smuggle.spec
```

Outcome: the file listed in `datas=[('/root/.ssh/id_rsa', '.')]` is copied into the `dist/` build output, where a lower-privileged user can read it — the standard escalation path against root-run PyInstaller build scripts.

