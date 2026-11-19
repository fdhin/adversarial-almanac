---
tool: "scoutsuite"
build-system: "scripts"
upstream: "https://github.com/nccgroup/ScoutSuite"
recipe: "blackarch"
nix-package: "scoutsuite"
source: "https://github.com/nccgroup/ScoutSuite"
license: "GPL-2.0-or-later"
blackarch-pkgver: "5.14.0.r0.g7909f2f"
---

# Installing scoutsuite

> Multi-Cloud Security Auditing Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scoutsuite) (version `5.14.0.r0.g7909f2f`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S scoutsuite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/ScoutSuite.git
cd ScoutSuite
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[scoutsuite]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#scoutsuite    # ad-hoc run, no install
nix profile install nixpkgs#scoutsuite   # or persist it
```
