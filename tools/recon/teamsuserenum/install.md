---
tool: "teamsuserenum"
build-system: "go"
upstream: "https://github.com/immunIT/TeamsUserEnum"
recipe: "blackarch"
source: "https://github.com/immunIT/TeamsUserEnum"
license: "GPL3"
blackarch-pkgver: "v1.0.r1.g0c8b6c2"
---

# Installing teamsuserenum

> User enumeration with Microsoft Teams API

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/teamsuserenum) (version `v1.0.r1.g0c8b6c2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S teamsuserenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/immunIT/TeamsUserEnum/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/immunIT/TeamsUserEnum.git
cd TeamsUserEnum
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  GOPATH="$srcdir" go mod tidy
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

Backlinks: [[recon]] · [[teamsuserenum]] · [[BlackArch Tools]]

