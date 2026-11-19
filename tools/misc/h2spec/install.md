---
tool: "h2spec"
build-system: "scripts"
upstream: "https://github.com/summerwind/h2spec"
recipe: "blackarch"
nix-package: "h2spec"
source: "https://github.com/summerwind/h2spec"
license: "MIT"
blackarch-pkgver: "2.6.0"
---

# Installing h2spec

> A conformance testing tool for HTTP/2 implementation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/h2spec) (version `2.6.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S h2spec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/summerwind/h2spec) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[h2spec]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#h2spec    # ad-hoc run, no install
nix profile install nixpkgs#h2spec   # or persist it
```
