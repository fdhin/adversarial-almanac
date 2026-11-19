---
tool: "dockerscan"
build-system: "go"
upstream: "https://github.com/cr0hn/dockerscan"
recipe: "blackarch"
source: "https://github.com/cr0hn/dockerscan"
license: "BSD-3-Clause"
blackarch-pkgver: "latest.r117.g8e5d3b3"
---

# Installing dockerscan

> Docker security analysis & hacking tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dockerscan) (version `latest.r117.g8e5d3b3`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S dockerscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/cr0hn/dockerscan/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/cr0hn/dockerscan) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  make build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dockerscan]] · [[BlackArch Tools]]

