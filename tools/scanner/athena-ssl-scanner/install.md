---
tool: "athena-ssl-scanner"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/93062/Athena-SSL-Cipher-Scanner.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/93062/Athena-SSL-Cipher-Scanner.html"
license: "GPL3"
blackarch-pkgver: "0.6.2"
---

# Installing athena-ssl-scanner

> A SSL cipher scanner that checks all cipher codes. It can identify about 150 different ciphers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/athena-ssl-scanner) (version `0.6.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S athena-ssl-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/93062/Athena-SSL-Cipher-Scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[athena-ssl-scanner]] · [[BlackArch Tools]]

