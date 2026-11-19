---
tool: "protos-sip"
build-system: "scripts"
upstream: "https://www.ee.oulu.fi/research/ouspg/PROTOS_Test-Suite_c07-sip"
recipe: "blackarch"
source: "https://www.ee.oulu.fi/research/ouspg/PROTOS_Test-Suite_c07-sip"
license: "GPL2"
blackarch-pkgver: "2"
kali-package: "protos-sip"
kali-version: "1.0-1kali6"
---

# Installing protos-sip

> SIP test suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/protos-sip) (version `2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S protos-sip    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install protos-sip    # Kali package version 1.0-1kali6
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.ee.oulu.fi/research/ouspg/PROTOS_Test-Suite_c07-sip) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `java-runtime-headless`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[protos-sip]] · [[BlackArch Tools]]

