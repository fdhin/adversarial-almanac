---
tool: "subover"
build-system: "go"
upstream: "https://github.com/Ice3man543/SubOver"
recipe: "blackarch"
source: "https://github.com/Ice3man543/SubOver"
license: "BSD"
blackarch-pkgver: "71.3d258e2"
---

# Installing subover

> A Powerful Subdomain Takeover Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subover) (version `71.3d258e2`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S subover    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/Ice3man543/SubOver/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/Ice3man543/SubOver.git
cd SubOver
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
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

Backlinks: [[scanner]] · [[subover]] · [[BlackArch Tools]]

