---
tool: "seclists"
build-system: "scripts"
upstream: "https://github.com/danielmiessler/SecLists/"
recipe: "blackarch"
nix-package: "seclists"
aur-package: "seclists"
source: "https://github.com/danielmiessler/SecLists/"
license: "custom:unknown"
blackarch-pkgver: "2026.2.r13.g00f80ec"
kali-package: "seclists"
kali-version: "2025.3-0kali1"
---

# Installing seclists

> A collection of multiple types of lists used during security assessments.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/seclists) (version `2026.2.r13.g00f80ec`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S seclists    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install seclists    # Kali package version 2025.3-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/danielmiessler/SecLists/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wordlist]] · [[seclists]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#seclists    # ad-hoc run, no install
nix profile install nixpkgs#seclists   # or persist it
```
