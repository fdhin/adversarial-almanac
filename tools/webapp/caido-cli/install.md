---
tool: "caido-cli"
build-system: "other"
upstream: "https://caido.io/"
recipe: "split-parent:caido"
nix-package: "caido-cli"
aur-package: "caido-cli"
source: "https://caido.io/"
license: "custom:private"
blackarch-pkgver: "0.57.1"
provides: ["caido-cli"]
---

# Installing caido-cli

> Intercepting proxy to replay, inject, scan and fuzz HTTP requests.

ℹ️ Packaged as a split package — built from the `caido` recipe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/caido) (version `0.57.1`, license custom:private).

## Arch / BlackArch

```bash
sudo pacman -S caido-cli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://caido.io/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
sed -i -E \
    "s|Exec=AppRun|Exec=env DESKTOPINTEGRATION=false /usr/bin/$pkgbase|" \
    "squashfs-root/$pkgbase.desktop"

    chmod -R a-x+rX squashfs-root/usr
```

## Installed layout (per recipe)

- Commands: `/usr/bin/caido-cli`
- Data: `/usr/share/applications`

Backlinks: [[webapp]] · [[caido-cli]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#caido-cli    # ad-hoc run, no install
nix profile install nixpkgs#caido-cli   # or persist it
```
