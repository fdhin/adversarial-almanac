---
tool: "gobd"
build-system: "go"
upstream: "https://github.com/razc411/GoBD"
recipe: "blackarch"
source: "https://github.com/razc411/GoBD"
license: "custom:unknown"
blackarch-pkgver: "82.3bbd17c"
---

# Installing gobd

> A Golang covert backdoor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gobd) (version `82.3bbd17c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gobd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/razc411/GoBD/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/razc411/GoBD.git
cd GoBD
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

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

Backlinks: [[backdoor]] · [[gobd]] · [[BlackArch Tools]]

