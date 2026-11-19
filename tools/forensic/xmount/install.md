---
tool: "xmount"
build-system: "cmake"
upstream: "https://www.sits.lu/xmount"
recipe: "kali-dsc"
aur-package: "xmount"
version: "1.3.1+ds-1"
---

# Installing xmount

> tool for crossmounting between disk image formats

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/xmount) (version `1.3.1+ds-1`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install xmount
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss xmount`); otherwise build from source below.

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://www.sits.lu/xmount) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Build dependencies (from source .dsc)

- `cmake`, `debhelper-compat`, `libafflib-dev`, `libbfio-dev`, `libewf-dev`, `libfuse3-dev`, `libgnutls28-dev`, `pkgconf`, `zlib1g-dev`
- Runtime deps: see [pkg.kali.org/pkg/xmount](https://pkg.kali.org/pkg/xmount)

Backlinks: [[forensic]] · [[xmount]] · [[Kali Gap Analysis]]
