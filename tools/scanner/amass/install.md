---
tool: "amass"
build-system: "go"
upstream: "https://github.com/OWASP/Amass"
recipe: "blackarch"
nix-package: "amass"
aur-package: "amass"
source: "https://github.com/OWASP/Amass"
license: "Apache-2.0"
blackarch-pkgver: "v5.1.3.r0.ga284d25"
kali-package: "amass"
kali-version: "5.1.1-0kali3"
---

# Installing amass

> In-depth subdomain enumeration written in Go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/amass) (version `v5.1.3.r0.ga284d25`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S amass    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install amass    # Kali package version 5.1.1-0kali3
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/OWASP/Amass/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/OWASP/Amass.git
cd Amass
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Exact build steps from the recipe

```bash
cd $pkgname

  export CGO_ENABLED=0

  GOPATH="$srcdir" go mod download
  GOPATH="$srcdir" go build \
    -trimpath \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags "-s -w" \
    -o $pkgname "./cmd/$pkgname"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[amass]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#amass    # ad-hoc run, no install
nix profile install nixpkgs#amass   # or persist it
```
