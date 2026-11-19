---
tool: "recon-ng"
build-system: "scripts"
upstream: "https://github.com/lanmaster53/recon-ng"
recipe: "blackarch"
nix-package: "recon-ng"
aur-package: "recon-ng"
source: "https://github.com/lanmaster53/recon-ng"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v5.1.2.r8.g32b08fe"
kali-package: "recon-ng"
kali-version: "5.1.2-2"
---

# Installing recon-ng

> A full-featured Web Reconnaissance framework written in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recon-ng) (version `v5.1.2.r8.g32b08fe`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S recon-ng    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install recon-ng    # Kali package version 5.1.2-2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lanmaster53/recon-ng) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dicttoxml`, `dnspython`, `flasgger`, `flask`, `flask-restful`, `jsonrpclib-pelix`, `lxml`, `mechanize`, `olefile`, `pyaml`, `pypdf2`, `requests`, `rq`, `slowaes`, `unicodecsv`, `xlsxwriter`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[recon-ng]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#recon-ng    # ad-hoc run, no install
nix profile install nixpkgs#recon-ng   # or persist it
```
