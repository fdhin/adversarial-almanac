---
tool: "jshell"
build-system: "scripts"
upstream: "https://github.com/s0md3v/JShell"
recipe: "blackarch"
source: "https://github.com/s0md3v/JShell"
license: "custom:unknown"
blackarch-pkgver: "7.ee3c92d"
---

# Installing jshell

> Get a JavaScript shell with XSS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jshell) (version `7.ee3c92d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/s0md3v/JShell.git
cd JShell
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jshell]] · [[BlackArch Tools]]

