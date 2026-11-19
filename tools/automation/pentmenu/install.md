---
tool: "pentmenu"
build-system: "scripts"
upstream: "https://github.com/GinjaChris/pentmenu"
recipe: "blackarch"
aur-package: "pentmenu"
source: "https://github.com/GinjaChris/pentmenu"
license: "GPL3"
blackarch-pkgver: "220.0cd7b58"
---

# Installing pentmenu

> A bash script for recon and DOS attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pentmenu) (version `220.0cd7b58`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pentmenu    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GinjaChris/pentmenu) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `hping`
  - `nmap`
  - `openbsd-netcat`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `stunnel`
  - `whois`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[pentmenu]] · [[BlackArch Tools]]

