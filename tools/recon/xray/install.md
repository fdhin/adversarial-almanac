---
tool: "xray"
build-system: "go"
upstream: "https://github.com/evilsocket/xray"
recipe: "blackarch"
nix-package: "xray"
aur-package: "xray"
source: "https://github.com/evilsocket/xray.git"
license: "GPL3"
blackarch-pkgver: "109.7e8cc67"
---

# Installing xray

> A tool for recon, mapping and OSINT gathering from public networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xray) (version `109.7e8cc67`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S xray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/evilsocket/xray/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/evilsocket/xray.git
cd xray
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `go-pie`
  - `golang-glide`

## Exact build steps from the recipe

```bash
mkdir -p "src/$_url" && rm -rf "src/$_url" && mv $pkgname "src/$_url" &&
    cd "src/$_url"

  GOPATH="$srcdir" go get -d -t $_url
  GOPATH="$srcdir" go get -u github.com/jteeuwen/go-bindata/...
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[xray]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#xray    # ad-hoc run, no install
nix profile install nixpkgs#xray   # or persist it
```
