---
tool: "afl++"
build-system: "make"
upstream: "https://aflplus.plus/"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/afl++/-/raw/main/PKGBUILD"
version: "5.00c"
license: "unknown"
---

# Installing afl++

> instrumentation-driven fuzzer for binary formats

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/afl++/-/raw/main/PKGBUILD) (version `5.00c`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S afl++
```

## Kali / Debian-based — same package name

```bash
sudo apt install afl++
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/AFLplusplus/AFLplusplus) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "AFLplusplus-${pkgver}"
  CFLAGS=${CFLAGS/-fcf-protection/} make PREFIX=/usr
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[fuzzer]] · [[afl++]] · [[Kali Gap Analysis]]
