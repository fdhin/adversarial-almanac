---
tool: "padbuster"
build-system: "perl"
upstream: "http://www.gdssecurity.com/l/t.php"
recipe: "blackarch"
nix-package: "padbuster"
source: "http://www.gdssecurity.com/l/t.php"
license: "custom:unknown"
blackarch-pkgver: "11.50e4a3e"
kali-package: "padbuster"
kali-version: "0.3.3+git20210818.50e4a3e-1kali1"
---

# Installing padbuster

> Automated script for performing Padding Oracle attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/padbuster) (version `11.50e4a3e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S padbuster    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install padbuster    # Kali package version 0.3.3+git20210818.50e4a3e-1kali1
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/GDSSecurity/PadBuster.git
cd PadBuster
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-bytes-random-secure`
  - `perl-crypt-ssleay`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[padbuster]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#padbuster    # ad-hoc run, no install
nix profile install nixpkgs#padbuster   # or persist it
```
