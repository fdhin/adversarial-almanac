---
name: "innounp"
categories:
  - blackarch-reversing
version: "0.50"
homepage: "https://sourceforge.net/projects/innounp/files/innounp/"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-05"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "WhatTheBlock/innounp"
github-stars: 63
github-pushed: "2021-09-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# innounp

> Unpacker for Inno Setup installers — lists, tests and extracts files and install script without running the installer.

## What it's for

Inno Setup is a widely used Windows installer generator and ships no official unpacker; the only vendor-supported way to get its files out is to run the installer. `innounp` reverses the installer format (Inno Setup versions 2.0.7 through 6.1.2) instead: it is a console application, built from the Inno Setup source code, that lists, tests and extracts the embedded files — and also recovers parts of the installation script (`.iss`), including registry changes and the compiled Pascal Script. The script recovery is usually the interesting part in a security review, since it shows exactly what the installer would do to the system. If an installation has a `setup.0` (built without SetupLdr), run the tool against `setup.0` instead of `setup.exe`.

## Typical use cases

### List installer contents

```
innounp -v installer.exe
```

Outcome: verbose listing of every embedded file with sizes and timestamps, preceded by general installation info — enough to triage what the installer drops without executing it.

### Extract everything into a directory

```
innounp -x -dunpacked installer.exe
```

Outcome: all embedded files are extracted into `unpacked/`, preserving the installer's directory structure, ready for static analysis (default extraction target is the current directory; `-d` overrides it).

### Extract a subset without paths

```
innounp -e installer.exe data\*.*
```

Outcome: only files matching the mask are extracted, flattened into the current directory — a quick way to grab one plugin or config subtree (to take all files from a directory use `dirname\*.*`, not just `dirname`).

### Test an installer's integrity

```
innounp -t installer.exe
```

Outcome: every embedded file is checked without writing anything — a fast verdict on truncated or corrupted downloads.

### Unpack a password-protected installer

```
innounp -x -pPassword1 installer.exe
```

Outcome: the installation is decrypted with the given password during extraction (`-ffile` reads the password from a file instead; add `-b` for non-interactive batch mode that never prompts).

*No corpus evidence — derived from upstream documentation.*
