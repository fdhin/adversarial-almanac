---
tool: "bettercap"
build-system: "go"
upstream: "https://github.com/bettercap/bettercap"
recipe: "arch-official/extra"
nix-package: "bettercap"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/bettercap/-/raw/main/PKGBUILD"
version: "2.41.7"
license: "GPL-3.0-only"
---

# Installing bettercap

> Complete, modular, portable and easily extensible MITM framework

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/bettercap/-/raw/main/PKGBUILD) (version `2.41.7`, license GPL-3.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S bettercap
```

## Kali / Debian-based — same package name

```bash
sudo apt install bettercap
```

## Nix (works on any distro)

```bash
nix run nixpkgs#bettercap    # ad-hoc run, no install
nix profile install nixpkgs#bettercap   # or persist it
```

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://www.bettercap.org/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/bettercap/bettercap.git
cd bettercap
go build ./...   # exact recipe flags below
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `iproute2`
  - `libnetfilter_queue`
  - `libnfnetlink`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libpcap.so`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `libusb-1.0.so`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}

  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"
  export CGO_REQUIRED="1"

  go build \
    -buildmode=pie \
    -mod=readonly \
    -modcacherw \
    -ldflags '-compressdwarf=false -linkmode=external' \
    .
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[sniffer]] · [[bettercap]] · [[Kali Gap Analysis]]
