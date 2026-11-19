---
tool: "spike"
build-system: "autotools"
upstream: "https://github.com/riscv/riscv-isa-sim"
recipe: "arch-official/extra"
nix-package: "spike"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/spike/-/raw/main/PKGBUILD"
version: "1.1.0"
license: "unknown"
---

# Installing spike

> Network protocol fuzzer

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/spike/-/raw/main/PKGBUILD) (version `1.1.0`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S spike
```

## Kali / Debian-based — same package name

```bash
sudo apt install spike
```

## Nix (works on any distro)

```bash
nix run nixpkgs#spike    # ad-hoc run, no install
nix profile install nixpkgs#spike   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/riscv/riscv-isa-sim.git
cd riscv-isa-sim
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd riscv-isa-sim
  ./configure --prefix=/usr
  make \
    CFLAGS="$CFLAGS -ffat-lto-objects -Wfatal-errors -w" \
    CXXFLAGS="$CXXFLAGS -ffat-lto-objects -Wfatal-errors -w" \
    LDFLAGS="$LDFLAGS -s -w"
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[fuzzer]] · [[spike]] · [[Kali Gap Analysis]]
