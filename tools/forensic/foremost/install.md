---
tool: "foremost"
build-system: "make"
upstream: "http://foremost.sourceforge.net/"
recipe: "arch-official/extra"
nix-package: "foremost"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/foremost/-/raw/main/PKGBUILD"
version: "1.5.7"
license: "custom"
---

# Installing foremost

> forensic program to recover lost files

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/foremost/-/raw/main/PKGBUILD) (version `1.5.7`, license custom).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S foremost
```

## Kali / Debian-based — same package name

```bash
sudo apt install foremost
```

## Nix (works on any distro)

```bash
nix run nixpkgs#foremost    # ad-hoc run, no install
nix profile install nixpkgs#foremost   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/foremost/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `patch`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "${srcdir}"/$pkgname-$pkgver
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[foremost]] · [[Kali Gap Analysis]]
