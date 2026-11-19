---
tool: "p0f"
build-system: "make"
upstream: "http://lcamtuf.coredump.cx/p0f3/"
recipe: "arch-official/extra"
nix-package: "p0f"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/p0f/-/raw/main/PKGBUILD"
version: "3.09b"
license: "GPL2"
---

# Installing p0f

> Passive OS fingerprinting tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/p0f/-/raw/main/PKGBUILD) (version `3.09b`, license GPL2).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S p0f
```

## Kali / Debian-based — same package name

```bash
sudo apt install p0f
```

## Nix (works on any distro)

```bash
nix run nixpkgs#p0f    # ad-hoc run, no install
nix profile install nixpkgs#p0f   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://lcamtuf.coredump.cx/p0f3/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  make
  cd tools
  make p0f-client
  make p0f-sendsyn
  make p0f-sendsyn6
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[p0f]] · [[Kali Gap Analysis]]
