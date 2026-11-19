---
tool: "brutex"
build-system: "scripts"
upstream: "https://github.com/1N3/BruteX"
recipe: "blackarch"
source: "https://github.com/1N3/BruteX"
license: "custom:unknown"
blackarch-pkgver: "114.eacd084"
---

# Installing brutex

> Automatically brute force all services running on a target.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brutex) (version `114.eacd084`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S brutex    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/1N3/BruteX.git
cd BruteX
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `dnsenum`
  - `hydra`
  - `masscan`
  - `net-snmp`
  - `nmap`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[brutex]] · [[BlackArch Tools]]

