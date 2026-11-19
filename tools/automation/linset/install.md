---
tool: "linset"
build-system: "scripts"
upstream: "https://github.com/vk496/linset"
recipe: "blackarch"
aur-package: "linset"
source: "https://github.com/vk496/linset"
license: "GPL3"
blackarch-pkgver: "9.8746b1f"
---

# Installing linset

> Evil Twin Attack Bash script - An automated WPA/WPA2 hacker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/linset) (version `9.8746b1f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S linset    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vk496/linset) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `curl`
  - `dhcp`
  - `gawk`
  - `hostapd`
  - `lighttpd`
  - `macchanger`
  - `mdk3`
  - `php-cgi`
  - `pyrit`
  - `python`
  - `unzip`
  - `wireless_tools` — Debian wireless-tools / Fedora wireless-tools
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[linset]] · [[BlackArch Tools]]

