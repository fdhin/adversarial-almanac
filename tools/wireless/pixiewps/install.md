---
tool: "pixiewps"
build-system: "make"
upstream: "https://github.com/wiire/pixiewps"
recipe: "arch-official/extra"
nix-package: "pixiewps"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/pixiewps/-/raw/main/PKGBUILD"
version: "1.4.2"
license: "GPL3"
---

# Installing pixiewps

> Offline WPS bruteforce tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/pixiewps/-/raw/main/PKGBUILD) (version `1.4.2`, license GPL3).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S pixiewps
```

## Kali / Debian-based — same package name

```bash
sudo apt install pixiewps
```

## Nix (works on any distro)

```bash
nix run nixpkgs#pixiewps    # ad-hoc run, no install
nix profile install nixpkgs#pixiewps   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/wiire/pixiewps.git
cd pixiewps
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  make OPENSSL=1 CFLAGS="${CFLAGS}"
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[pixiewps]] · [[Kali Gap Analysis]]
