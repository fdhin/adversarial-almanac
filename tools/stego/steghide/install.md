---
tool: "steghide"
build-system: "autotools"
upstream: "https://steghide.sourceforge.net/"
recipe: "kali-dsc"
nix-package: "steghide"
aur-package: "steghide"
version: "0.5.1+git20240220-2"
---

# Installing steghide

> steganography hiding tool

Recipe data: Kali source package [`.dsc`](https://pkg.kali.org/pkg/steghide) (version `0.5.1+git20240220-2`) — full debian/ tree not in Kali's GitLab.

## Kali / Debian-based

```bash
sudo apt install steghide
```

## Arch / BlackArch

Not packaged. Check the AUR (`paru -Ss steghide`); otherwise build from source below.

## Nix (works on any distro)

```bash
nix run nixpkgs#steghide    # ad-hoc run, no install
nix profile install nixpkgs#steghide   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://steghide.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Build dependencies (from source .dsc)

- `debhelper-compat`, `doxygen`, `libjpeg-dev`, `libmcrypt-dev`, `libmhash-dev`, `libtool-bin`, `zlib1g-dev`
- Runtime deps: see [pkg.kali.org/pkg/steghide](https://pkg.kali.org/pkg/steghide)

Backlinks: [[stego]] · [[steghide]] · [[Kali Gap Analysis]]
