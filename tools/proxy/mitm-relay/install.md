---
tool: "mitm-relay"
build-system: "scripts"
upstream: "https://github.com/jrmdev/mitm_relay"
recipe: "blackarch"
source: "https://github.com/jrmdev/mitm_relay"
license: "Apache"
blackarch-pkgver: "41.2d168b2"
---

# Installing mitm-relay

> Hackish way to intercept and modify non-HTTP protocols through Burp & others.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mitm-relay) (version `41.2d168b2`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S mitm-relay    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jrmdev/mitm_relay.git
cd mitm_relay
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[mitm-relay]] · [[BlackArch Tools]]

