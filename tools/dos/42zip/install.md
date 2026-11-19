---
tool: "42zip"
build-system: "scripts"
upstream: "http://blog.fefe.de/?ts=b6cea88d"
recipe: "blackarch"
source: "http://www.securityfocus.com/data/vulnerabilities/exploits/42.zip"
license: "custom:unknown"
blackarch-pkgver: "42"
---

# Installing 42zip

> Recursive Zip archive bomb.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/42zip) (version `42`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S 42zip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO http://www.securityfocus.com/data/vulnerabilities/exploits/42.zip
tar xf 42.zip
cd 42
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[42zip]] · [[BlackArch Tools]]

