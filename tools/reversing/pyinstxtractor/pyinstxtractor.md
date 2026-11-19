---
name: "pyinstxtractor"
categories:
  - blackarch-reversing
version: "2026.07.r0.g815d31c"
homepage: "https://github.com/extremecoders-re/pyinstxtractor"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "extremecoders-re/pyinstxtractor"
github-stars: 4520
github-pushed: "2026-07-22"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# pyinstxtractor

> Unpacks PyInstaller-built executables back into `.pyc` bytecode files that a decompiler can turn into source.

## What it's for

PyInstaller bundles a Python program into a single self-extracting executable; pyinstxtractor reverses that packaging. It is a standalone Python script that unpacks the CArchive and PYZ containers inside a PyInstaller executable — Windows PE or Linux ELF, PyInstaller 2.0 through the 6.x series — writing every embedded module to an `<name>_extracted` directory and fixing the `.pyc` headers so bytecode decompilers recognize them. Reach for it whenever a binary shows PyInstaller markers (`pyi_rth_` strings, a `MEI` temp directory at runtime, a "Cannot open PyInstaller archive" error). It recovers bytecode, not source: the standard follow-up is a decompiler such as uncompyle6 or Decompyle++ (`pycdc`), and extracting with the same Python version that built the executable avoids unmarshalling errors on the PYZ archive.

## Typical use cases

### Extract a PyInstaller-packed executable

```
python3 pyinstxtractor.py installer.exe
```

Outcome: prints the detected PyInstaller and Python versions and the package size, then unpacks the archive into `installer.exe_extracted/` — the entry-point script lands there as a `.pyc` (named in the "Possible entry point" lines) alongside the bundled libraries, and the PYZ archive is expanded into `PYZ-00.pyz_extracted/`.

### Match the extractor to the target's Python version

```
python3.7 pyinstxtractor.py legacy.exe
```

Outcome: extracts using the interpreter line that matches the version pyinstxtractor reports for the executable (e.g. "Python version: 37"), avoiding unmarshalling errors when the PYZ contains old bytecode — run a different `python3.x` binary rather than a flag.

### Decompile the recovered bytecode

```
uncompyle6 installer.exe_extracted/installer.pyc
```

Outcome: reconstructs readable Python source from the entry-point bytecode; modules taken from `PYZ-00.pyz_extracted/` decompile the same way. Decompyle++ (`pycdc`) handles newer Python versions that uncompyle6 rejects.
