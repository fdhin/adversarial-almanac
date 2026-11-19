---
tool: "mboxgrep"
build-system: "autotools"
upstream: "http://mboxgrep.sourceforge.net/"
recipe: "blackarch"
source: "http://mboxgrep.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "0.7.9"
---

# Installing mboxgrep

> A small, non-interactive utility that scans mail folders for messages matching regular expressions. It does matching against basic and extended POSIX regular expressions, and reads and writes a variety of mailbox formats.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mboxgrep) (version `0.7.9`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S mboxgrep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://mboxgrep.sourceforge.net) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bzip2`
  - `pcre`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --mandir="$pkgdir/usr/share/man" \
    --infodir="$pkgdir/usr/share/info"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/info`, `/usr/share/man`

Backlinks: [[forensic]] · [[mboxgrep]] · [[BlackArch Tools]]

