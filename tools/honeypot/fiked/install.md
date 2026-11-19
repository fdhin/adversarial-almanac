---
tool: "fiked"
build-system: "make"
upstream: "https://www.roe.ch/FakeIKEd"
recipe: "blackarch"
source: "https://www.roe.ch/FakeIKEd"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.0.5"
kali-package: "fiked"
kali-version: "0.0.5-1kali8+b1"
---

# Installing fiked

> Fake IDE daemon.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fiked) (version `0.0.5`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S fiked    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install fiked    # Kali package version 0.0.5-1kali8+b1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.roe.ch/FakeIKEd) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libgcrypt`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  PREFIX=/usr make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[honeypot]] · [[fiked]] · [[BlackArch Tools]]

