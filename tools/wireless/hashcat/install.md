---
tool: "hashcat"
build-system: "make"
upstream: "https://hashcat.net/hashcat"
recipe: "arch-official/extra"
nix-package: "hashcat"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/hashcat/-/raw/main/PKGBUILD"
version: "8.0.0"
license: "MIT"
---

# Installing hashcat

> World's fastest and most advanced password recovery utility

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/hashcat/-/raw/main/PKGBUILD) (version `8.0.0`, license MIT).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S hashcat
```

## Kali / Debian-based — same package name

```bash
sudo apt install hashcat
```

## Nix (works on any distro)

```bash
nix run nixpkgs#hashcat    # ad-hoc run, no install
nix profile install nixpkgs#hashcat   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://hashcat.net/hashcat/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  rm -rf deps/{OpenCL-Headers,xxHash}
  export CFLAGS+=" ${CPPFLAGS}"
  export CXXFLAGS+=" ${CPPFLAGS}"
  make \
    PREFIX=/usr \
    SHARED=1 \
    MAINTAINER_MODE=1 \
    USE_SYSTEM_XXHASH=1 \
    USE_SYSTEM_OPENCL=1 \
    USE_SYSTEM_ZLIB=1
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[hashcat]] · [[Kali Gap Analysis]]
