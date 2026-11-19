---
tool: "codeql"
build-system: "scripts"
upstream: "https://github.com/codeql"
recipe: "blackarch"
nix-package: "codeql"
aur-package: "codeql"
source: "https://github.com/codeql"
license: "custom"
blackarch-pkgver: "2.22.4"
---

# Installing codeql

> The CLI tool for GitHub CodeQL

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/codeql) (version `2.22.4`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S codeql    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/codeql) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `java-runtime-headless`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[codeql]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#codeql    # ad-hoc run, no install
nix profile install nixpkgs#codeql   # or persist it
```
