---
tool: "htcap"
build-system: "scripts"
upstream: "https://github.com/segment-srl/htcap"
recipe: "blackarch"
source: "https://github.com/segment-srl/htcap"
license: "GPL3"
blackarch-pkgver: "155.a59c592"
---

# Installing htcap

> A web application analysis tool for detecting communications between javascript and the server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/htcap) (version `155.a59c592`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S htcap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/segment-srl/htcap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `simplejson`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `phantomjs`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[htcap]] · [[BlackArch Tools]]

