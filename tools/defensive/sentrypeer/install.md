---
tool: "sentrypeer"
build-system: "make"
upstream: "https://github.com/SentryPeer/SentryPeer"
recipe: "blackarch"
source: "https://github.com/SentryPeer/SentryPeer"
license: "GPL"
blackarch-pkgver: "v3.0.0.r14.g02173c8"
kali-package: "sentrypeer"
kali-version: "4.0.4-0kali3"
---

# Installing sentrypeer

> Protect SIP Servers from bad actors.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sentrypeer) (version `v3.0.0.r14.g02173c8`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sentrypeer    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sentrypeer    # Kali package version 4.0.4-0kali3
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/SentryPeer/SentryPeer) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `autoconf-archive`
  - `cmocka`
  - `curl`
  - `fmt`
  - `http-parser`
  - `jansson`
  - `libmicrohttpd`
  - `libosip2`
  - `opendht-c`
  - `pcre2`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `uuid`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[sentrypeer]] · [[BlackArch Tools]]

