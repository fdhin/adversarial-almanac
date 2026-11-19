---
tool: "sliver"
build-system: "go"
upstream: "https://github.com/BishopFox/sliver"
recipe: "blackarch"
aur-package: "sliver"
source: "https://github.com/BishopFox/sliver"
license: "BSD-3-Clause"
blackarch-pkgver: "v1.7.9.r0.g6cf3ad7"
---

# Installing sliver

> Opensource C2 framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sliver) (version `v1.7.9.r0.g6cf3ad7`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S sliver    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/BishopFox/sliver/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/BishopFox/sliver) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `unzip`
  - `zip`

## Exact build steps from the recipe

```bash
make --directory $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`, `/usr/share/fish`, `/usr/share/zsh`

Backlinks: [[backdoor]] · [[sliver]] · [[BlackArch Tools]]

