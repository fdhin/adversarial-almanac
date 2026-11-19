---
tool: "proxytunnel"
build-system: "make"
upstream: "https://github.com/proxytunnel/proxytunnel"
recipe: "arch-official/extra"
nix-package: "proxytunnel"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/proxytunnel/-/raw/main/PKGBUILD"
version: "1.13.0"
license: "GPL-2.0-only WITH cryptsetup-OpenSSL-exception, BSD-3-Clause"
---

# Installing proxytunnel

> Help SSH and other protocols through HTTP(S) proxies

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/proxytunnel/-/raw/main/PKGBUILD) (version `1.13.0`, license GPL-2.0-only WITH cryptsetup-OpenSSL-exception, BSD-3-Clause).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S proxytunnel
```

## Kali / Debian-based — same package name

```bash
sudo apt install proxytunnel
```

## Nix (works on any distro)

```bash
nix run nixpkgs#proxytunnel    # ad-hoc run, no install
nix profile install nixpkgs#proxytunnel   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/proxytunnel/proxytunnel.git
cd proxytunnel
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `asciidoc`
  - `glibc`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `xmlto`

## Exact build steps from the recipe

```bash
cd "$pkgname"
  CFLAGS+=' -fcommon' # https://wiki.gentoo.org/wiki/Gcc_10_porting_notes/fno_common
  make prefix=/usr
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[backdoor]] · [[proxytunnel]] · [[Kali Gap Analysis]]
