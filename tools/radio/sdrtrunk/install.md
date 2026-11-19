---
tool: "sdrtrunk"
build-system: "scripts"
upstream: "https://github.com/DSheirer/sdrtrunk"
recipe: "blackarch"
source: "https://github.com/DSheirer/sdrtrunk"
license: "GPL3"
blackarch-pkgver: "0.6.0"
provides: ["sdr-trunk"]
---

# Installing sdrtrunk

> A cross-platform java application for decoding, monitoring, recording and streaming trunked mobile and related radio protocols using SDR.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sdrtrunk) (version `0.6.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sdrtrunk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DSheirer/sdrtrunk) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `at-spi2-core`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel

## Installed layout (per recipe)

- Commands: `/usr/bin/sdr-trunk`
- Data: `/usr/share/applications`

Backlinks: [[radio]] · [[sdrtrunk]] · [[BlackArch Tools]]

