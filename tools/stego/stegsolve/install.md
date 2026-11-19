---
tool: "stegsolve"
build-system: "scripts"
upstream: "https://github.com/zardus/ctf-tools/blob/master/stegsolve/install"
recipe: "blackarch"
nix-package: "stegsolve"
aur-package: "stegsolve"
source: "http://www.caesum.com/handbook/Stegsolve.jar"
license: "custom"
blackarch-pkgver: "1.3"
---

# Installing stegsolve

> Steganography Solver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stegsolve) (version `1.3`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S stegsolve    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/zardus/ctf-tools/blob/master/stegsolve/install) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[stego]] · [[stegsolve]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#stegsolve    # ad-hoc run, no install
nix profile install nixpkgs#stegsolve   # or persist it
```
