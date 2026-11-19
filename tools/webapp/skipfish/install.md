---
tool: "skipfish"
build-system: "scripts"
upstream: "http://code.google.com/p/skipfish/"
recipe: "blackarch"
aur-package: "skipfish"
source: "http://code.google.com/p/skipfish/"
license: "Apache"
blackarch-pkgver: "2.10b"
kali-package: "skipfish"
kali-version: "2.10b-2kali8"
---

# Installing skipfish

> A fully automated, active web application security reconnaissance tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/skipfish) (version `2.10b`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S skipfish    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install skipfish    # Kali package version 2.10b-2kali8
```

## Any Linux distro

**From source:**

```bash
curl -fLO skipfish-bin.tar.xz
tar xf skipfish-bin.tar.xz
cd skipfish-bin
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libidn`
  - `libidn11`
  - `openssl-1.0`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[webapp]] · [[skipfish]] · [[BlackArch Tools]]

