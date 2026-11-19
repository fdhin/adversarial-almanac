---
tool: "cewl"
build-system: "scripts"
upstream: "http://www.digininja.org/projects/cewl.php"
recipe: "blackarch"
nix-package: "cewl"
source: "http://www.digininja.org/projects/cewl.php"
license: "CC-BY-NC-SA-2.0, GPL-3.0-or-later"
blackarch-pkgver: "6.2.1.r33.gca01aea"
provides: ["cewl-fab"]
kali-package: "cewl"
kali-version: "6.2.1-1"
---

# Installing cewl

> A custom word list generator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cewl) (version `6.2.1.r33.gca01aea`, license CC-BY-NC-SA-2.0, GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S cewl    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install cewl    # Kali package version 6.2.1-1
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/digininja/CeWL.git
cd CeWL
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- Commands: `/usr/bin/cewl-fab`

Backlinks: [[automation]] · [[cewl]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cewl    # ad-hoc run, no install
nix profile install nixpkgs#cewl   # or persist it
```
