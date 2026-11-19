---
tool: "ipsumdump"
ebuild-package: "ipsumdump"
build-system: "autotools"
upstream: "https://github.com/kohler/ipsumdump"
recipe: "archstrike-pkgbuild"
aur-package: "ipsumdump"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ipsumdump"
version: "1.86"
license: "GPL2, custom:Click"
---

# Installing ipsumdump

> A tool that summarizes TCP/IP dump files into a self-describing ASCII format easily readable by humans and programs

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ipsumdump) (version `1.86`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S ipsumdump
```

Also on the AUR: `paru -S ipsumdump` (version `1.86-1`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/kohler/ipsumdump) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}" || exit
  ./configure --prefix='/usr' --mandir='/usr/share/man' --sbindir='/usr/bin'
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[sniffer]] · [[ipsumdump]] · [[ArchStrike Gap Analysis]]
