---
tool: "paros"
build-system: "scripts"
upstream: "http://www.parosproxy.org"
recipe: "blackarch"
aur-package: "paros"
source: "http://www.parosproxy.org"
license: "GPL2"
blackarch-pkgver: "3.2.13"
kali-package: "paros"
kali-version: "3.2.13-1kali6"
---

# Installing paros

> Java-based HTTP/HTTPS proxy for assessing web app vulnerabilities. Supports editing/viewing HTTP messages on-the-fly, spiders, client certificates, proxy-chaining, intelligent scanning for XSS and SQLi, etc.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/paros) (version `3.2.13`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S paros    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install paros    # Kali package version 3.2.13-1kali6
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.parosproxy.org) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/paros`

Backlinks: [[webapp]] · [[paros]] · [[BlackArch Tools]]

