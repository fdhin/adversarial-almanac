---
tool: "bytecode-viewer"
build-system: "scripts"
upstream: "https://github.com/Konloch/bytecode-viewer"
recipe: "blackarch"
nix-package: "bytecode-viewer"
aur-package: "bytecode-viewer"
source: "https://github.com/Konloch/bytecode-viewer"
license: "custom:unknown"
blackarch-pkgver: "2.12"
kali-package: "bytecode-viewer"
kali-version: "2.13.2-0kali1"
---

# Installing bytecode-viewer

> A Java 8/Android APK Reverse Engineering Suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bytecode-viewer) (version `2.12`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bytecode-viewer    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install bytecode-viewer    # Kali package version 2.13.2-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Konloch/bytecode-viewer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[bytecode-viewer]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bytecode-viewer    # ad-hoc run, no install
nix profile install nixpkgs#bytecode-viewer   # or persist it
```
