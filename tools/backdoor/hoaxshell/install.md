---
tool: "hoaxshell"
build-system: "scripts"
upstream: "https://github.com/t3l3machus/hoaxshell"
recipe: "blackarch"
source: "https://github.com/t3l3machus/hoaxshell"
license: "BSD-2-Clause"
blackarch-pkgver: "150.e1bba89"
---

# Installing hoaxshell

> A Windows reverse shell payload generator and handler that abuses the http(s) protocol to establish a beacon-like reverse shell.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hoaxshell) (version `150.e1bba89`, license BSD-2-Clause).

## Arch / BlackArch

```bash
sudo pacman -S hoaxshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/t3l3machus/hoaxshell) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `gnureadline`, `pyperclip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ipython`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[hoaxshell]] · [[BlackArch Tools]]

