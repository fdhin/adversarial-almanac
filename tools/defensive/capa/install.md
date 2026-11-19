---
tool: "capa"
build-system: "scripts"
upstream: "https://github.com/mandiant/capa"
recipe: "blackarch"
nix-package: "capa"
source: "https://github.com/mandiant/capa"
license: "Apache-2.0"
blackarch-pkgver: "7.4.0"
---

# Installing capa

> The FLARE team's open-source tool to identify capabilities in executable files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/capa) (version `7.4.0`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S capa    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mandiant/capa) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[capa]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#capa    # ad-hoc run, no install
nix profile install nixpkgs#capa   # or persist it
```
