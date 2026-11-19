---
name: "intensio-obfuscator"
categories:
  - blackarch-misc
version: "280.f66a22b"
homepage: "https://github.com/Hnfull/Intensio-Obfuscator"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "Hnfull/Intensio-Obfuscator"
github-stars: 648
github-pushed: "2020-07-25"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# intensio-obfuscator

> Obfuscate Python 2 and 3 source trees by mangling identifiers, padding junk code, and stripping comments.

## What it's for

Intensio-Obfuscator takes a directory of Python source files and rewrites them into hard-to-read equivalents: variable/class/function names replaced by random strings of a chosen length, optional padding of random dead code, comments and blank lines removed, and (for Python 2 sources only) strings converted to hex escapes. The output stays functionally equivalent and runnable. The author describes the result as a weak obfuscation layer on its own, meant to be combined with other techniques, and the project is no longer maintained. Use it to slow down reading of Python code you own — and note that certain code patterns are unsupported, so always test the obfuscated output.

## Typical use cases

### Obfuscate a source tree with the default features

Comments, blank lines, and stray `.pyc` files are removed without touching any names.

```
intensio-obfuscator -i src-project/ -o src-obfuscated/
```

Outcome: the empty output directory is filled with an obfuscated copy of every source file; behavior is unchanged, but comments and spacing are gone.

### Rename identifiers aggressively

Replace variable/class/function names with 128-character random strings.

```
intensio-obfuscator -i src-project/ -o src-obfuscated/ -mlen high -rts
```

Outcome: same program with every identifier swapped for random 128-character strings (`-mlen lower|medium|high` selects 32/64/128); words that must survive renaming can be listed in a file passed via `--excludewords`.

### Layer padding and file-name mangling

Max out the layers for a multi-file project.

```
intensio-obfuscator -i src-project/ -o src-obfuscated/ -mlen medium -rts -ps -rfn -ind 4
```

Outcome: identifiers are randomized, random junk snippets are injected after lines, and the source files themselves are renamed; `-ind` must match the source's indentation (2, 4, or 8 spaces) or parsing fails.

### Watch what it does

```
intensio-obfuscator -i src-project/ -o src-obfuscated/ -rts -v
```

Outcome: verbose mode prints each obfuscation step as it runs — useful for spotting which file hit an unsupported pattern.

*No corpus evidence — derived from upstream documentation.*
