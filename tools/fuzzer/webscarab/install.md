---
tool: "webscarab"
build-system: "scripts"
upstream: "http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project"
recipe: "blackarch"
source: "http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project"
license: "GPL2"
blackarch-pkgver: "20120422.001828"
kali-package: "webscarab"
kali-version: "20200519-d22bd60-0kali2"
---

# Installing webscarab

> Framework for analysing applications that communicate using the HTTP and HTTPS protocols

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webscarab) (version `20120422.001828`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S webscarab    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install webscarab    # Kali package version 20200519-d22bd60-0kali2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[webscarab]] · [[BlackArch Tools]]

