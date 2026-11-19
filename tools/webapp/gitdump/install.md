---
tool: "gitdump"
build-system: "scripts"
upstream: "https://github.com/Ebryx/GitDump"
recipe: "blackarch"
source: "https://github.com/Ebryx/GitDump"
license: "custom:unknown"
blackarch-pkgver: "1.a8b6188"
---

# Installing gitdump

> A pentesting tool that dumps the source code from .git even when the directory traversal is disabled.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitdump) (version `1.a8b6188`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gitdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ebryx/GitDump.git
cd GitDump
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[gitdump]] · [[BlackArch Tools]]

