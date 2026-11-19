---
tool: "nuclei-templates"
build-system: "scripts"
upstream: "https://github.com/projectdiscovery/nuclei-templates"
recipe: "blackarch"
nix-package: "nuclei-templates"
aur-package: "nuclei-templates"
source: "https://github.com/projectdiscovery/nuclei-templates"
license: "MIT"
blackarch-pkgver: "v10.5.1.r408.ge6adf54"
---

# Installing nuclei-templates

> Community curated list of template files for the nuclei engine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nuclei-templates) (version `v10.5.1.r408.ge6adf54`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S nuclei-templates    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/projectdiscovery/nuclei-templates) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[nuclei-templates]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#nuclei-templates    # ad-hoc run, no install
nix profile install nixpkgs#nuclei-templates   # or persist it
```
