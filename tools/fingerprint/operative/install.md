---
tool: "operative"
build-system: "go"
upstream: "https://github.com/graniet/operative-framework"
recipe: "blackarch"
source: "https://github.com/graniet/operative-framework"
license: "Apache"
blackarch-pkgver: "148.163acdf"
---

# Installing operative

> Framework based on fingerprint action, this tool is used for get information on a website or a enterprise target with multiple modules (Viadeo search,Linkedin search, Reverse email whois, Reverse ip whois, SQL file forensics ...).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/operative) (version `148.163acdf`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S operative    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/graniet/operative-framework/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/graniet/operative-framework.git
cd operative-framework
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod init go.mod
  GOPATH="$srcdir" go mod edit -replace gopkg.in/ahmdrz/goinsta.v2@v2.4.5=github.com/ahmdrz/goinsta/v2@v2.4.5
  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" CGO_ENABLED=1 go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[operative]] · [[BlackArch Tools]]

