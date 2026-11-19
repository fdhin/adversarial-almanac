---
tool: "hash-identifier"
build-system: "scripts"
upstream: "https://github.com/blackploit/hash-identifier"
recipe: "blackarch"
nix-package: "hash-identifier"
aur-package: "hash-identifier"
source: "https://github.com/blackploit/hash-identifier"
license: "custom:unknown"
blackarch-pkgver: "6.0e08a97"
kali-package: "hash-identifier"
kali-version: "1.2+git20180314-0kali3"
---

# Installing hash-identifier

> Software to identify the different types of hashes used to encrypt data and especially passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hash-identifier) (version `6.0e08a97`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hash-identifier    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install hash-identifier    # Kali package version 1.2+git20180314-0kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/blackploit/hash-identifier) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hash-identifier]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hash-identifier    # ad-hoc run, no install
nix profile install nixpkgs#hash-identifier   # or persist it
```
