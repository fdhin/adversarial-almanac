---
tool: "nsec3map"
build-system: "python"
upstream: "https://github.com/anonion0/nsec3map"
recipe: "blackarch"
source: "https://github.com/anonion0/nsec3map"
license: "GPL-3.0-only"
blackarch-pkgver: "v0.8.2.r0.gd145b13"
---

# Installing nsec3map

> A tool to enumerate the resource records of a DNS zone using its DNSSEC NSEC or NSEC3 chain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nsec3map) (version `v0.8.2.r0.gd145b13`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S nsec3map    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/anonion0/nsec3map) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `dnspython`, `installer`, `numpy`, `scipy`, `setuptools`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`

Backlinks: [[recon]] · [[nsec3map]] · [[BlackArch Tools]]

