---
tool: "cowpatty"
build-system: "make"
upstream: "https://github.com/joswr1ght/cowpatty"
recipe: "arch-official/extra"
nix-package: "cowpatty"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/cowpatty/-/raw/main/PKGBUILD"
version: "4.8"
license: "GPL"
---

# Installing cowpatty

> Brute-force WPA dictionary attack

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/cowpatty/-/raw/main/PKGBUILD) (version `4.8`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S cowpatty
```

## Kali / Debian-based — same package name

```bash
sudo apt install cowpatty
```

## Nix (works on any distro)

```bash
nix run nixpkgs#cowpatty    # ad-hoc run, no install
nix profile install nixpkgs#cowpatty   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/joswr1ght/cowpatty.git
cd cowpatty
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  CFLAGS+=" ${CPPFLAGS}"
  make all strip
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[cowpatty]] · [[Kali Gap Analysis]]
