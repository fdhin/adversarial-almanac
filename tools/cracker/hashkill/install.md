---
tool: "hashkill"
build-system: "autotools"
upstream: "http://www.gat3way.eu/hashkill"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-2"
---

# Installing hashkill

> Multi-threaded password recovery tool with multi-GPU support

Recipe source of truth: Pentoo [`hashkill.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-crypt/hashkill) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask hashkill
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss hashkill`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.gat3way.eu/hashkill) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/json-c`, `virtual/opencl-sdk`, `x11-drivers/ati-drivers`, `x11-drivers/nvidia-drivers`

Backlinks: [[cracker]] · [[hashkill]] · [[Pentoo Gap Analysis]]
