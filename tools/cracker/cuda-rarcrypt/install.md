---
tool: "cuda-rarcrypt"
build-system: "scripts"
upstream: "http://www.crark.net/"
recipe: "pentoo-ebuild"
version: "3.2-r1"
license: "GPL-3"
---

# Installing cuda-rarcrypt

> A Nvidia GPU-based RAR bruteforcer

Recipe source of truth: Pentoo [`cuda-rarcrypt.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-crypt/cuda-rarcrypt) (version `3.2-r1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask cuda-rarcrypt
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss cuda-rarcrypt`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.crark.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `app-arch/unrar`, `dev-util/nvidia-cuda-sdk`, `x11-drivers/nvidia-drivers`

Backlinks: [[cracker]] · [[cuda-rarcrypt]] · [[Pentoo Gap Analysis]]
