---
tool: "phoss"
build-system: "make"
upstream: "http://194.150.168.69/phoss"
recipe: "blackarch"
source: "http://194.150.168.69/phoss"
license: "custom"
blackarch-pkgver: "0.1.13"
---

# Installing phoss

> Sniffer designed to find HTTP, FTP, LDAP, Telnet, IMAP4, VNC and POP3 logins.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phoss) (version `0.1.13`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S phoss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://194.150.168.69/phoss) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $_pkgname

  make OPTIMI="$CFLAGS -fcommon"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[phoss]] · [[BlackArch Tools]]

