---
tool: "cloud-buster"
build-system: "scripts"
upstream: "https://github.com/SageHack/cloud-buster"
recipe: "blackarch"
aur-package: "cloud-buster"
source: "https://github.com/SageHack/cloud-buster"
license: "GPL"
blackarch-pkgver: "194.b55e4a1"
---

# Installing cloud-buster

> A tool that checks Cloudflare enabled sites for origin IP leaks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cloud-buster) (version `194.b55e4a1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S cloud-buster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SageHack/cloud-buster) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `dnspython`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[cloud-buster]] · [[BlackArch Tools]]

