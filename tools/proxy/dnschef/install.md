---
tool: "dnschef"
build-system: "scripts"
upstream: "http://thesprawl.org/projects/dnschef/"
recipe: "blackarch"
nix-package: "dnschef"
source: "http://thesprawl.org/projects/dnschef/"
license: "GPL3"
blackarch-pkgver: "17.a395411"
kali-package: "dnschef"
kali-version: "0.4+git20190327-0kali4"
---

# Installing dnschef

> A highly configurable DNS proxy for pentesters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnschef) (version `17.a395411`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dnschef    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dnschef    # Kali package version 0.4+git20190327-0kali4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://thesprawl.org/projects/dnschef/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnslib`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[dnschef]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnschef    # ad-hoc run, no install
nix profile install nixpkgs#dnschef   # or persist it
```
