---
tool: "iw"
build-system: "make"
upstream: "https://wireless.wiki.kernel.org/en/users/documentation/iw"
recipe: "arch-official/core"
nix-package: "iw"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/iw/-/raw/main/PKGBUILD"
version: "6.17"
license: "GPL"
---

# Installing iw

> tool for configuring Linux wireless devices

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/iw/-/raw/main/PKGBUILD) (version `6.17`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S iw
```

## Kali / Debian-based — same package name

```bash
sudo apt install iw
```

## Nix (works on any distro)

```bash
nix run nixpkgs#iw    # ad-hoc run, no install
nix profile install nixpkgs#iw   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/jberg/iw.git?signed
cd iw.git?signed
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libnl`
  - `linux-api-headers`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}/"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[iw]] · [[Kali Gap Analysis]]
