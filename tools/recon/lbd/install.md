---
tool: "lbd"
build-system: "scripts"
upstream: "http://ge.mine.nu/code/lbd"
recipe: "blackarch"
nix-package: "lbd"
aur-package: "lbd"
source: "http://ge.mine.nu/code/lbd"
license: "GPL"
blackarch-pkgver: "20130719"
kali-package: "lbd"
kali-version: "0.4-1kali3"
---

# Installing lbd

> Load Balancing detector,

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lbd) (version `20130719`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S lbd    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install lbd    # Kali package version 0.4-1kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://ge.mine.nu/code/lbd) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `dnsutils`
  - `openbsd-netcat`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[lbd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#lbd    # ad-hoc run, no install
nix profile install nixpkgs#lbd   # or persist it
```
