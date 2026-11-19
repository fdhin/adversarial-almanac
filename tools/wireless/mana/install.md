---
tool: "mana"
build-system: "make"
upstream: "https://github.com/sensepost/mana"
recipe: "blackarch"
source: "https://github.com/sensepost/mana"
license: "BSD, GPL3"
blackarch-pkgver: "78.438d1fe"
provides: ["mana-firelamb-view", "mana-nat-full", "mana-nat-simple", "mana-noupstream", "mana-noupstream-eap"]
---

# Installing mana

> A toolkit for rogue access point (evilAP) attacks first presented at Defcon 22.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mana) (version `78.438d1fe`, license BSD, GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mana    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/sensepost/mana) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `apache`
  - `asleap`
  - `dsniff`
  - `iptables`
  - `libnl1`
  - `macchanger`
  - `metasploit`
  - `procps`
  - `sslsplit`
  - `stunnel`
  - `tinyproxy`
- ⚠️ Python 2 only deps: python2-dnspython, python2-pcapy, python2-scapy (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd $pkgname

  git submodule init
  git submodule update

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/mana-firelamb-view`, `/usr/bin/mana-nat-full`, `/usr/bin/mana-nat-simple`, `/usr/bin/mana-noupstream`, `/usr/bin/mana-noupstream-eap`
- Data: `/usr/share/mana-toolkit`

Backlinks: [[wireless]] · [[mana]] · [[BlackArch Tools]]

