---
tool: "voipong"
build-system: "scripts"
upstream: "http://www.enderunix.org/voipong/"
recipe: "blackarch"
source: "http://www.enderunix.org/voipong/"
license: "GPL"
blackarch-pkgver: "2.0"
---

# Installing voipong

> A utility which detects all Voice Over IP calls on a pipeline, and for those which are G711 encoded, dumps actual conversation to separate wave files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/voipong) (version `2.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S voipong    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.enderunix.org/voipong/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[voipong]] · [[BlackArch Tools]]

