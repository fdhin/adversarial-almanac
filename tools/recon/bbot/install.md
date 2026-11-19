---
tool: "bbot"
build-system: "scripts"
upstream: "https://github.com/blacklanternsecurity/bbot"
recipe: "blackarch"
nix-package: "bbot"
source: "https://github.com/blacklanternsecurity/bbot"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v3.0.2.r0.ga6fb827"
---

# Installing bbot

> Multipurpose scanner built to automate your Recon, Bug Bounties, and ASM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bbot) (version `v3.0.2.r0.ga6fb827`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S bbot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/blacklanternsecurity/bbot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[bbot]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bbot    # ad-hoc run, no install
nix profile install nixpkgs#bbot   # or persist it
```
