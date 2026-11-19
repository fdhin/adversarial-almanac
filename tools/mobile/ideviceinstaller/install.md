---
tool: "ideviceinstaller"
build-system: "autotools"
upstream: "http://www.libimobiledevice.org/"
recipe: "pentoo-ebuild"
nix-package: "ideviceinstaller"
aur-package: "ideviceinstaller"
version: "1.1.0"
license: "GPL-2"
---

# Installing ideviceinstaller

> A tool to interact with the installation_proxy of an Apple's iDevice

Recipe source of truth: Pentoo [`ideviceinstaller.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-pda/ideviceinstaller) (version `1.1.0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask ideviceinstaller
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss ideviceinstaller`).

## Nix (works on any distro)

```bash
nix run nixpkgs#ideviceinstaller    # ad-hoc run, no install
nix profile install nixpkgs#ideviceinstaller   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.libimobiledevice.org/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `app-pda/libimobiledevice-1.1.7`, `app-pda/libplist-1.12`, `dev-libs/libzip-0.8`, `virtual/pkgconfig`

Backlinks: [[mobile]] · [[ideviceinstaller]] · [[Pentoo Gap Analysis]]
