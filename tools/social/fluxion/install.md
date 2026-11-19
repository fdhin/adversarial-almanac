---
tool: "fluxion"
build-system: "scripts"
upstream: "https://github.com/FluxionNetwork/fluxion"
recipe: "blackarch"
source: "https://github.com/FluxionNetwork/fluxion"
license: "GPL3"
blackarch-pkgver: "v6.32.r8.gf064d24"
---

# Installing fluxion

> A security auditing and social-engineering research tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fluxion) (version `v6.32.r8.gf064d24`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fluxion    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/FluxionNetwork/fluxion) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `awk`
  - `bash`
  - `bc`
  - `binutils`
  - `coreutils`
  - `cowpatty`
  - `curl`
  - `dhcp`
  - `dsniff`
  - `hostapd`
  - `iw`
  - `lighttpd`
  - `macchanger`
  - `mdk3`
  - `mdk4`
  - `nmap`
  - `p7zip`
  - `php-cgi`
  - `unzip`
  - `xorg-xdpyinfo`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[social]] · [[fluxion]] · [[BlackArch Tools]]

