---
tool: "fl0p"
build-system: "other"
upstream: "http://lcamtuf.coredump.cx/"
recipe: "blackarch"
source: "http://lcamtuf.coredump.cx/"
license: "GPL2"
blackarch-pkgver: "0.1"
---

# Installing fl0p

> A passive L7 flow fingerprinter that examines TCP/UDP/ICMP packet sequences, can peek into cryptographic tunnels, can tell human beings and robots apart, and performs a couple of other infosec-related tricks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fl0p) (version `0.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S fl0p    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://lcamtuf.coredump.cx/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gzip`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./Build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[fingerprint]] · [[fl0p]] · [[BlackArch Tools]]

