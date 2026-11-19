---
tool: "ligolo-mp"
build-system: "go"
upstream: "https://github.com/ttpreport/ligolo-mp"
recipe: "blackarch"
source: "https://github.com/ttpreport/ligolo-mp"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v2.2.1.r2.g7da8994"
---

# Installing ligolo-mp

> Multiplayer pivoting solution.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ligolo-mp) (version `v2.2.1.r2.g7da8994`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ligolo-mp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ttpreport/ligolo-mp/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/ttpreport/ligolo-mp) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `awk`
  - `curl`
  - `gnupg`
  - `zip`

## Exact build steps from the recipe

```bash
cd $pkgname

  make assets
  make server client
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[ligolo-mp]] · [[BlackArch Tools]]

