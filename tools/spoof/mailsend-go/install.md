---
tool: "mailsend-go"
build-system: "go"
upstream: "https://github.com/muquit/mailsend-go"
recipe: "blackarch"
source: "https://github.com/muquit/mailsend-go"
license: "MIT"
blackarch-pkgver: "156.07ba45e"
---

# Installing mailsend-go

> A multi-platform command line tool to send mail via SMTP protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mailsend-go) (version `156.07ba45e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mailsend-go    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/muquit/mailsend-go/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/muquit/mailsend-go) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

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

Backlinks: [[spoof]] · [[mailsend-go]] · [[BlackArch Tools]]

