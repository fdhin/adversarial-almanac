---
tool: "goshs"
build-system: "go"
upstream: "https://github.com/goshs-labs/goshs"
recipe: "blackarch"
nix-package: "goshs"
source: "https://github.com/goshs-labs/goshs"
license: "MIT"
blackarch-pkgver: "2.1.0"
---

# Installing goshs

> A single-binary file server for pentesters and sysadmins with HTTP/S, WebDAV, SFTP, SMB, LDAP, NTLM hash capture, DNS/SMTP callbacks and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goshs) (version `2.1.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S goshs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/goshs-labs/goshs/releases) for a static binary.

**B. Build from source (needs `go`):**

**Source:** download the release from the [upstream site](https://github.com/goshs-labs/goshs) (the recipe uses a versioned archive).

```bash
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `go`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" CGO_ENABLED=0 go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname .
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[goshs]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#goshs    # ad-hoc run, no install
nix profile install nixpkgs#goshs   # or persist it
```
