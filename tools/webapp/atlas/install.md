---
tool: "atlas"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/Atlas"
recipe: "blackarch"
nix-package: "atlas"
aur-package: "atlas"
source: "https://github.com/m4ll0k/Atlas"
license: "GPL3"
blackarch-pkgver: "7.77bd6c8"
---

# Installing atlas

> Open source tool that can suggest sqlmap tampers to bypass WAF/IDS/IPS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/atlas) (version `7.77bd6c8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S atlas    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m4ll0k/Atlas.git
cd Atlas
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `humanfriendly`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[atlas]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#atlas    # ad-hoc run, no install
nix profile install nixpkgs#atlas   # or persist it
```
