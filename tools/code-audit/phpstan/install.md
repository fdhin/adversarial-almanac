---
tool: "phpstan"
build-system: "scripts"
upstream: "https://github.com/phpstan/phpstan"
recipe: "blackarch"
nix-package: "phpstan"
source: "https://github.com/phpstan/phpstan"
license: "MIT"
blackarch-pkgver: "15664.16a9b494e"
---

# Installing phpstan

> PHP Static Analysis Tool - discover bugs in your code without running it.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phpstan) (version `15664.16a9b494e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S phpstan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/phpstan/phpstan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[phpstan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#phpstan    # ad-hoc run, no install
nix profile install nixpkgs#phpstan   # or persist it
```
