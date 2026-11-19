---
tool: "gomapenum"
build-system: "go"
upstream: "https://github.com/nodauf/GoMapEnum"
recipe: "blackarch"
nix-package: "gomapenum"
source: "https://github.com/nodauf/GoMapEnum"
license: "GPL3"
blackarch-pkgver: "v1.1.0.r114.g5aee194"
---

# Installing gomapenum

> User enumeration and password bruteforce on Azure, ADFS, OWA, O365, Teams and gather emails on Linkedin.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gomapenum) (version `v1.1.0.r114.g5aee194`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gomapenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/nodauf/GoMapEnum/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/nodauf/GoMapEnum.git
cd GoMapEnum
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  GOPATH="$srcdir" go mod tidy
  GOPATH="$srcdir" go mod download

  cd src/
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

Backlinks: [[cracker]] · [[gomapenum]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gomapenum    # ad-hoc run, no install
nix profile install nixpkgs#gomapenum   # or persist it
```
