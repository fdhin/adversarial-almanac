---
tool: "mphidflash"
build-system: "make"
upstream: "https://code.google.com/p/mphidflash/"
recipe: "pentoo-ebuild"
aur-package: "mphidflash"
version: "1.6"
license: "GPL-3"
---

# Installing mphidflash

> ommand-line tool for communicating with the USB-Bootloader in PIC microcontrollers

Recipe source of truth: Pentoo [`mphidflash.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/dev-embedded/mphidflash) (version `1.6`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask mphidflash
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss mphidflash`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://code.google.com/p/mphidflash/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/libhid`, `virtual/libusb`

Backlinks: [[hardware]] · [[mphidflash]] · [[Pentoo Gap Analysis]]
