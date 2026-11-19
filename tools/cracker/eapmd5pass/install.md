---
tool: "eapmd5pass"
build-system: "make"
upstream: "http://www.willhackforsushi.com/?page_id=67"
recipe: "blackarch"
source: "http://www.willhackforsushi.com/?page_id=67"
license: "GPL2"
blackarch-pkgver: "3.3d5551f"
kali-package: "eapmd5pass"
kali-version: "1.5-0kali1+b1"
---

# Installing eapmd5pass

> An implementation of an offline dictionary attack against the EAP-MD5 protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eapmd5pass) (version `3.3d5551f`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S eapmd5pass    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install eapmd5pass    # Kali package version 1.5-0kali1+b1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.willhackforsushi.com/?page_id=67) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make utils.o
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[eapmd5pass]] · [[BlackArch Tools]]

