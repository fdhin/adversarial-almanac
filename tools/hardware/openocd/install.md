---
tool: "openocd"
build-system: "autotools"
upstream: "https://openocd.org"
recipe: "arch-official/extra"
nix-package: "openocd"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/openocd/-/raw/main/PKGBUILD"
version: "0.12.0"
license: "unknown"
---

# Installing openocd

> Open on-chip JTAG/SWD debug solution for embedded target devices

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/openocd/-/raw/main/PKGBUILD) (version `0.12.0`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S openocd
```

## Kali / Debian-based — same package name

```bash
sudo apt install openocd
```

## Nix (works on any distro)

```bash
nix run nixpkgs#openocd    # ad-hoc run, no install
nix profile install nixpkgs#openocd   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/openocd-org/openocd
cd openocd
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname"

  local _features=(
    aice
    amtjtagaccel
    armjtagew
    at91rm9200
    buspirate
    capstone
    cmsis-dap
    dummy
    ep93xx
    ftdi
    gw16012
    ioutil
    jlink
    jtag_vpi
    legacy-ft2232_libftdi
    oocd_trace
    opendous
    openjtag_ftdi
    osbdm
    parport
    presto_libftdi
    remote-bitbang
    rlink
    stlink
    sysfsgpio
    ti-icdi
    ulink
    usb-blaster-2
    usb_blaster_libftdi
    usbprog
    vsllink
    xlnx_pcie_xvc
  )

  ./configure \
    --prefix=/usr \
    --disable-werror \
    --disable-internal-jimtcl \
    --disable-internal-libjaylink \
    ${_features[@]/#/--enable-}

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[hardware]] · [[openocd]] · [[Kali Gap Analysis]]
