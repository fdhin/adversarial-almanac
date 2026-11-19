---
tool: "subzy"
build-system: "go"
upstream: "https://github.com/PentestPad/subzy"
recipe: "blackarch"
source: "https://github.com/PentestPad/subzy"
license: "GPL-2.0-or-later"
blackarch-pkgver: "v1.2.1.r0.gf2e2ec7"
---

# Installing subzy

> Subdomain takeover vulnerability checker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subzy) (version `v1.2.1.r0.gf2e2ec7`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S subzy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/PentestPad/subzy/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/PentestPad/subzy) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[subzy]] · [[BlackArch Tools]]

