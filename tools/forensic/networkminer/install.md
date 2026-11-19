---
tool: "networkminer"
build-system: "scripts"
upstream: "http://www.netresec.com/"
recipe: "blackarch"
nix-package: "networkminer"
aur-package: "networkminer"
source: "http://www.netresec.com/"
license: "GPL2"
blackarch-pkgver: "3.2"
---

# Installing networkminer

> A Network Forensic Analysis Tool for advanced Network Traffic Analysis, sniffer and packet analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/networkminer) (version `3.2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S networkminer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.netresec.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `mono`
- **System libraries** (Arch name — translate for your distro):
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`

Backlinks: [[forensic]] · [[networkminer]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#networkminer    # ad-hoc run, no install
nix profile install nixpkgs#networkminer   # or persist it
```
