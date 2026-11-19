---
tool: "air"
build-system: "perl"
upstream: "https://sourceforge.net/projects/air-imager/"
recipe: "blackarch"
nix-package: "air"
aur-package: "air"
source: "https://sourceforge.net/projects/air-imager/"
license: "GPL"
blackarch-pkgver: "2.0.0"
---

# Installing air

> A GUI front-end to dd/dc3dd designed for easily creating forensic images.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/air) (version `2.0.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S air    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/air-imager/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl-tk`
  - `sharutils`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/air`

Backlinks: [[forensic]] · [[air]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#air    # ad-hoc run, no install
nix profile install nixpkgs#air   # or persist it
```
