---
tool: "sulley"
build-system: "scripts"
upstream: "https://github.com/OpenRCE/sulley/"
recipe: "blackarch"
source: "https://github.com/OpenRCE/sulley/"
license: "GPL2"
blackarch-pkgver: "1.0.bff0dd1"
---

# Installing sulley

> A pure-python fully automated and unattended fuzzing framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sulley) (version `1.0.bff0dd1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sulley    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/OpenRCE/sulley/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `python2`
- ⚠️ Python 2 only deps: python2-pcapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[sulley]] · [[BlackArch Tools]]

