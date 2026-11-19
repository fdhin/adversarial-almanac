---
name: "python-uncompyle6"
categories:
  - blackarch-decompiler
version: "3.9.3"
homepage: "https://pypi.org/project/uncompyle6/#files"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-16"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "rocky/python-uncompyle6"
github-stars: 4328
github-pushed: "2026-10-05"
github-archived: false
github-issues: 42
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# python-uncompyle6

> Cross-version Python bytecode decompiler covering Python 1.0 through 3.8.

## What it's for

uncompyle6 translates compiled Python bytecode (`.pyc`/`.pyo` files) back into equivalent Python source code, accepting bytecode from Python 1.0 through 3.8 plus some PyPy and old Dropbox variants. Reach for it when you hold compiled modules but no source: recovering logic from a shipped `.pyc`, auditing frozen or embedded modules, or reading what was extracted from a bundled application. It decompiles bytecode only — it does not unpack Py2EXE or PyInstaller executables (extract the `.pyc` first), and it does not handle Cython or obfuscated bytecode such as PJOrion. For Python 3.7–3.8 the successor decompyle3 generally produces better output; bytecode from Python 3.9+ is out of scope.

## Typical use cases

### Decompile one bytecode file to the terminal

Recover the source of a single module.

```
uncompyle6 module.pyc
```

Outcome: prints the reconstructed Python source of module.pyc to stdout, including the module docstring and top-level statements.

### Decompile several files into a directory

Recover multiple modules to disk at once.

```
uncompyle6 -o recovered/ module.pyc other.pyc
```

Outcome: writes the recovered source as `recovered/module.pyc_dis` and `recovered/other.pyc_dis` — the `_dis` suffix marks decompiled output, and files the tool cannot handle get a `_failed` suffix instead.

### Walk a tree of bytecode files

Recover a whole dropped application in one pass.

```
uncompyle6 -r -o recovered/ ./extracted
```

Outcome: recurses through ./extracted, finds every `.pyc` and `.pyo`, and decompiles each into the output directory — the practical way to bulk-recover a frozen or bundled Python program.

### Verify the reconstruction

Gain confidence in the recovered source.

```
uncompyle6 --verify syntax module.pyc
```

Outcome: after decompiling, the generated source is compiled by the matching interpreter to syntax-check the reconstruction, flagging output that is not valid Python rather than presenting it as fact; `--verify run` additionally executes the result and checks the exit code.

*No corpus evidence — derived from upstream documentation.*
