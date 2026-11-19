---
tool: "winregfs"
build-system: "make"
upstream: "https://codeberg.org/jbruchon/winregfs"
recipe: "kali-dsc"
version: "0.8-2"
---

# Installing winregfs

> Windows registry FUSE filesystem

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/winregfs) (version `0.8-2`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install winregfs
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss winregfs`); otherwise build from source below.

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://codeberg.org/jbruchon/winregfs) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `libfuse3-dev`, `pkgconf`
- Runtime deps: see [pkg.kali.org/pkg/winregfs](https://pkg.kali.org/pkg/winregfs)

Backlinks: [[forensic]] · [[winregfs]] · [[Kali Gap Analysis]]
