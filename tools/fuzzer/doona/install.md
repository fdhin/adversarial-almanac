---
tool: "doona"
build-system: "perl"
upstream: "https://github.com/wireghoul/doona"
recipe: "blackarch"
nix-package: "doona"
source: "https://github.com/wireghoul/doona"
license: "GPL2"
blackarch-pkgver: "145.7a4796c"
---

# Installing doona

> A fork of the Bruteforce Exploit Detector Tool (BED).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/doona) (version `145.7a4796c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S doona    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/wireghoul/doona) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[doona]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#doona    # ad-hoc run, no install
nix profile install nixpkgs#doona   # or persist it
```
