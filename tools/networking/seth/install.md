---
tool: "seth"
build-system: "scripts"
upstream: "https://github.com/SySS-Research/Seth"
recipe: "blackarch"
source: "https://github.com/SySS-Research/Seth"
license: "MIT"
blackarch-pkgver: "106.6e869cd"
---

# Installing seth

> Perform a MitM attack and extract clear text credentials from RDP connections.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/seth) (version `106.6e869cd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S seth    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SySS-Research/Seth.git
cd Seth
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dsniff`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`
  - `tcpdump`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[seth]] · [[BlackArch Tools]]

