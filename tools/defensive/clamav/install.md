---
tool: "clamav"
build-system: "cmake"
upstream: "https://www.clamav.net/"
recipe: "arch-official/extra"
nix-package: "clamav"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/clamav/-/raw/main/PKGBUILD"
version: "1.5.4"
license: "unknown"
---

# Installing clamav

> anti-virus utility for Unix - command-line interface

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/clamav/-/raw/main/PKGBUILD) (version `1.5.4`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S clamav
```

## Kali / Debian-based — same package name

```bash
sudo apt install clamav
```

## Nix (works on any distro)

```bash
nix run nixpkgs#clamav    # ad-hoc run, no install
nix profile install nixpkgs#clamav   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://www.clamav.net/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  # OpenSSL 4.1 hashing fixes are still unmerged upstream.
  # CMake also forwards these OpenSSL paths to the Rust bindings.
  cmake \
    -DCMAKE_BUILD_TYPE='None' \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_INSTALL_SBINDIR=bin \
    -DAPP_CONFIG_DIRECTORY=/etc/clamav \
    -DDATABASE_DIRECTORY=/var/lib/clamav \
    -DENABLE_EXTERNAL_MSPACK=ON \
    -DOPENSSL_INCLUDE_DIR=/usr/include/openssl-3 \
    -DOPENSSL_CRYPTO_LIBRARY=/usr/lib/openssl-3/libcrypto.so \
    -DOPENSSL_SSL_LIBRARY=/usr/lib/openssl-3/libssl.so \
    -Wno-dev \
    -B build \
    -G Ninja \
    -S .
  ninja -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[clamav]] · [[Kali Gap Analysis]]
