---
tool: "parsero"
build-system: "scripts"
upstream: "https://github.com/behindthefirewalls/Parsero"
recipe: "blackarch"
nix-package: "parsero"
source: "https://github.com/behindthefirewalls/Parsero"
license: "GPL2"
blackarch-pkgver: "81.e5b585a"
---

# Installing parsero

> A robots.txt audit tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/parsero) (version `81.e5b585a`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S parsero    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/behindthefirewalls/Parsero.git
cd Parsero
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[parsero]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#parsero    # ad-hoc run, no install
nix profile install nixpkgs#parsero   # or persist it
```
