---
tool: "pytbull"
build-system: "scripts"
upstream: "https://github.com/netrunn3r/pytbull-ng"
recipe: "blackarch"
source: "https://github.com/netrunn3r/pytbull-ng"
license: "GPL3"
blackarch-pkgver: "19.3d82a54"
---

# Installing pytbull

> Next generation of pytbull, IDS/IPS testing framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pytbull) (version `19.3d82a54`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pytbull    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/netrunn3r/pytbull-ng) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cherrypy`, `feedparser`, `m2crypto`, `paramiko`, `requests`, `scapy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[pytbull]] · [[BlackArch Tools]]

