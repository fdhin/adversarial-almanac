---
tool: "dnsteal"
build-system: "scripts"
upstream: "https://github.com/m57/dnsteal"
recipe: "blackarch"
source: "https://github.com/m57/dnsteal"
license: "GPL2"
blackarch-pkgver: "28.1b09d21"
---

# Installing dnsteal

> DNS Exfiltration tool for stealthily sending files over DNS requests..

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsteal) (version `28.1b09d21`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dnsteal    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m57/dnsteal) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[dnsteal]] · [[BlackArch Tools]]

