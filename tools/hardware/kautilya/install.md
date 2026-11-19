---
tool: "kautilya"
build-system: "scripts"
upstream: "https://github.com/samratashok/Kautilya/releases"
recipe: "blackarch"
source: "https://github.com/samratashok/Kautilya/releases"
license: "GPL3"
blackarch-pkgver: "52.1c9d5b0"
---

# Installing kautilya

> Pwnage with Human Interface Devices using Teensy++2.0 and Teensy 3.0 devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kautilya) (version `52.1c9d5b0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S kautilya    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/samratashok/Kautilya.git
cd Kautilya
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[hardware]] · [[kautilya]] · [[BlackArch Tools]]

