---
tool: "sonar-scanner"
build-system: "scripts"
upstream: "https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/"
recipe: "blackarch"
aur-package: "sonar-scanner"
source: "https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/"
license: "LGPL3"
blackarch-pkgver: "6.2.1.4610"
---

# Installing sonar-scanner

> Generic CLI tool to launch project analysis on SonarQube servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sonar-scanner) (version `6.2.1.4610`, license LGPL3).

## Arch / BlackArch

```bash
sudo pacman -S sonar-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `java-runtime`
  - `sh`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[sonar-scanner]] · [[BlackArch Tools]]

