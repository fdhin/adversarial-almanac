---
tool: "stews"
build-system: "scripts"
upstream: "https://github.com/PalindromeLabs/STEWS"
recipe: "blackarch"
source: "https://github.com/PalindromeLabs/STEWS"
license: "Apache"
blackarch-pkgver: "1.0.0.r7.gc7bba5a"
provides: ["stews-discovery", "stews-fingerprint", "stews-vuln-detect"]
---

# Installing stews

> A Security Tool for Enumerating WebSockets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stews) (version `1.0.0.r7.gc7bba5a`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S stews    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PalindromeLabs/STEWS.git
cd STEWS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cprint`, `requests`, `websocket-client`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `python`
  - `zgrab`

## Installed layout (per recipe)

- Commands: `/usr/bin/stews-discovery`, `/usr/bin/stews-fingerprint`, `/usr/bin/stews-vuln-detect`

Backlinks: [[webapp]] · [[stews]] · [[BlackArch Tools]]

