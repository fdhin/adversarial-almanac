---
name: "yay"
categories:
  - blackarch-misc
version: "13.0.1"
homepage: "https://github.com/Jguer/yay"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: go
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Jguer/yay"
github-stars: 13823
github-pushed: "2026-11-02"
github-archived: false
github-issues: 226
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 152
gh-activity-checked: "2026-11-09"
---

# yay

> Yet Another Yogurt — a pacman wrapper and AUR helper written in Go: install, build and upgrade Arch-family packages (including the Arch User Repository) with dependency solving and interactive search menus.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Package management, not offense: yay wraps pacman and adds everything the Arch User Repository needs — interactive search menus, PKGBUILD download and review before build, dependency solving across repo and AUR packages, and full-system upgrades that include AUR packages. It earns its place in a pentest toolbox as the standard way to keep bleeding-edge tooling installed and current on Arch/BlackArch workstations (it is packaged by the BlackArch repo itself). It does nothing beyond Arch-family package management — no other distribution's package format, and it is not a hardening or auditing tool.

## Typical use cases

### Search and install interactively

```
yay smbmap
```

Outcome: presents a numbered package-installation selection menu spanning the official repos and the AUR; choosing an entry downloads the PKGBUILD, shows its diff for review, resolves dependencies, builds and installs it. Extra words narrow the search: `yay linux header` first searches `linux`, then narrows on `header`.

### Upgrade the whole system, AUR included

```
yay -Syu --devel
```

Outcome: syncs repos and AUR, then upgrades every package; `--devel` additionally rebuilds VCS/`-git` packages whose upstream has new commits (after a one-time `yay -Y --gendb`). Bare `yay` is an alias for `yay -Syu`.

### Build and install a local PKGBUILD

```
yay -Bi .
```

Outcome: resolves and installs the dependencies of the PKGBUILD in the current directory, builds it and installs the result — the shortcut for installing a tool from a hand-written or unreleased PKGBUILD.

### Pull a PKGBUILD for inspection

```
yay -G fireprox
```

Outcome: downloads the PKGBUILD from ABS or AUR into a local directory, letting you review exactly what a package builds and runs before installing it.
