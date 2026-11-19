---
tool: "pcredz"
build-system: "scripts"
upstream: "https://github.com/lgandx/PCredz"
recipe: "blackarch"
source: "https://github.com/lgandx/PCredz"
license: "GPL2"
blackarch-pkgver: "108.a07051d"
---

# Installing pcredz

> A tool that extracts credit card numbers and more from a pcap file or from a live interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pcredz) (version `108.a07051d`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pcredz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lgandx/PCredz.git
cd PCredz
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `python-libpcap`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[pcredz]] · [[BlackArch Tools]]

