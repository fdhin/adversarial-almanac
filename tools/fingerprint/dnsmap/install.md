---
tool: "dnsmap"
build-system: "scripts"
upstream: "https://github.com/pagvac/dnsmap"
recipe: "blackarch"
nix-package: "dnsmap"
aur-package: "dnsmap"
source: "https://github.com/pagvac/dnsmap"
license: "GPL"
blackarch-pkgver: "51.d727cfb"
kali-package: "dnsmap"
kali-version: "0.36-4"
---

# Installing dnsmap

> Passive DNS network mapper.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsmap) (version `51.d727cfb`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dnsmap    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dnsmap    # Kali package version 0.36-4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pagvac/dnsmap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[dnsmap]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnsmap    # ad-hoc run, no install
nix profile install nixpkgs#dnsmap   # or persist it
```
