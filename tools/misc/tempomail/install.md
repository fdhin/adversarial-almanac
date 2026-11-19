---
tool: "tempomail"
build-system: "go"
upstream: "https://github.com/kavishgr/tempomail"
recipe: "blackarch"
source: "https://github.com/kavishgr/tempomail"
license: "MIT"
blackarch-pkgver: "26.5600ec3"
---

# Installing tempomail

> Tool to create a temporary email address in 1 Second and receive emails.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tempomail) (version `26.5600ec3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tempomail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/kavishgr/tempomail/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/kavishgr/tempomail) (the recipe uses a versioned archive).

```bash
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

Backlinks: [[misc]] · [[tempomail]] · [[BlackArch Tools]]

