---
tool: "python2-capstone"
build-system: "make"
upstream: "https://github.com/aquynh/capstone"
recipe: "blackarch"
source: "https://github.com/aquynh/capstone"
license: "BSD"
blackarch-pkgver: "4.0.1"
kali-package: "python3-capstone"
kali-version: "5.0.9-1"
---

# Installing python2-capstone

> A disassembly framework with the target of becoming the ultimate disasm engine for binary analysis and reversing in the security community.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python2-capstone) (version `4.0.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S python2-capstone    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install python3-capstone    # Kali package version 5.0.9-1
```

(Debian/Kali name the package `python3-capstone`, not `python2-capstone`.)

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/aquynh/capstone) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `glibc`
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[python2-capstone]] · [[BlackArch Tools]]

