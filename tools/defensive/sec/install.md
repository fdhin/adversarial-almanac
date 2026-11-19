---
tool: "sec"
ebuild-package: "sec"
build-system: "perl"
upstream: "https://simple-evcorr.github.io/"
recipe: "archstrike-pkgbuild"
nix-package: "sec"
aur-package: "sec"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sec"
version: "2.9.0"
license: "GPL"
---

# Installing sec

> A logfile monitoring tool with support for event correlation, written in perl

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sec) (version `2.9.0`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S sec
```

Also on the AUR: `paru -S sec` (version `2.9.4-1`).

## Nix (works on any distro)

```bash
nix run nixpkgs#sec    # ad-hoc run, no install
nix profile install nixpkgs#sec   # or persist it
```

## Any other Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://simple-evcorr.github.io/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[sec]] · [[ArchStrike Gap Analysis]]
