---
tool: "minimodem"
build-system: "autotools"
upstream: "https://github.com/kamalmostafa/minimodem"
recipe: "blackarch"
nix-package: "minimodem"
aur-package: "minimodem"
source: "https://github.com/kamalmostafa/minimodem"
license: "GPL3"
blackarch-pkgver: "353.bb2f34c"
---

# Installing minimodem

> A command-line program which decodes (or generates) audio modem tones at any specified baud rate, using various framing protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/minimodem) (version `353.bb2f34c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S minimodem    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/kamalmostafa/minimodem) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `fftw`
  - `libpulse`
  - `sndio`

## Exact build steps from the recipe

```bash
cd $pkgname

  aclocal
  autoheader
  automake --add-missing
  autoconf

  ./configure --prefix=/usr --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[minimodem]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#minimodem    # ad-hoc run, no install
nix profile install nixpkgs#minimodem   # or persist it
```
