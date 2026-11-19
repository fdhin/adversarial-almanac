---
tool: "easy-creds"
build-system: "scripts"
upstream: "https://github.com/brav0hax/easy-creds"
recipe: "blackarch"
source: "https://github.com/brav0hax/easy-creds"
license: "GPL2"
blackarch-pkgver: "45.bf9f00c"
---

# Installing easy-creds

> A bash script that leverages ettercap and other tools to obtain credentials.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/easy-creds) (version `45.bf9f00c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S easy-creds    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/brav0hax/easy-creds) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `asleap`
  - `bash`
  - `dhcp`
  - `dsniff`
  - `ettercap`
  - `freeradius`
  - `hamster`
  - `hostapd`
  - `ipcalc`
  - `mdk3`
  - `metasploit`
  - `screen`
  - `sslstrip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[easy-creds]] · [[BlackArch Tools]]

