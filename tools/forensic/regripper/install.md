---
tool: "regripper"
build-system: "perl"
upstream: "https://github.com/keydet89/RegRipper3.0"
recipe: "blackarch"
nix-package: "regripper"
source: "https://github.com/keydet89/RegRipper3.0"
license: "MIT"
blackarch-pkgver: "119.ec96dd4"
kali-package: "regripper"
kali-version: "3.0~git20260527.ec96dd4+dfsg-1"
---

# Installing regripper

> Open source forensic software used as a Windows Registry data extraction command line or GUI tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/regripper) (version `119.ec96dd4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S regripper    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install regripper    # Kali package version 3.0~git20260527.ec96dd4+dfsg-1
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/keydet89/RegRipper3.0
cd RegRipper3.0
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-parse-registry`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[regripper]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#regripper    # ad-hoc run, no install
nix profile install nixpkgs#regripper   # or persist it
```
