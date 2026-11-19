---
tool: "tcpflow"
build-system: "autotools"
upstream: "https://github.com/simsong/tcpflow"
recipe: "arch-official/extra"
nix-package: "tcpflow"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/tcpflow/-/raw/main/PKGBUILD"
version: "1.6.1"
license: "GPL-3.0-only"
---

# Installing tcpflow

> TCP flow recorder

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/tcpflow/-/raw/main/PKGBUILD) (version `1.6.1`, license GPL-3.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S tcpflow
```

## Kali / Debian-based — same package name

```bash
sudo apt install tcpflow
```

## Nix (works on any distro)

```bash
nix run nixpkgs#tcpflow    # ad-hoc run, no install
nix profile install nixpkgs#tcpflow   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/simsong/tcpflow.git
cd tcpflow
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `cairo`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd $pkgname
  sh bootstrap.sh
  ./configure --prefix=/usr --mandir=/usr/share/man
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[tcpflow]] · [[Kali Gap Analysis]]
