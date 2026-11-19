---
tool: "ohrwurm"
build-system: "make"
upstream: "http://mazzoo.de/"
recipe: "blackarch"
source: "http://mazzoo.de/"
license: "GPL"
blackarch-pkgver: "1.7a1182a"
kali-package: "ohrwurm"
kali-version: "0.1-1kali4+b1"
---

# Installing ohrwurm

> A small and simple RTP fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ohrwurm) (version `1.7a1182a`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ohrwurm    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ohrwurm    # Kali package version 0.1-1kali4+b1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://mazzoo.de/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[ohrwurm]] · [[BlackArch Tools]]

