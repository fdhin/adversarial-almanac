---
tool: "ridrelay"
build-system: "scripts"
upstream: "https://github.com/skorov/ridrelay"
recipe: "blackarch"
source: "https://github.com/skorov/ridrelay"
license: "GPL3"
blackarch-pkgver: "34.f2fa99c"
---

# Installing ridrelay

> Enumerate usernames on a domain where you have no creds by using SMB Relay with low priv.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ridrelay) (version `34.f2fa99c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ridrelay    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/skorov/ridrelay) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ridrelay]] · [[BlackArch Tools]]

