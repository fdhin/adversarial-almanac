---
tool: "pureblood"
build-system: "scripts"
upstream: "https://github.com/cr4shcod3/pureblood"
recipe: "blackarch"
source: "https://github.com/cr4shcod3/pureblood"
license: "MIT"
blackarch-pkgver: "37.2c5ce07"
---

# Installing pureblood

> A Penetration Testing Framework created for Hackers / Pentester / Bug Hunter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pureblood) (version `37.2c5ce07`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pureblood    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cr4shcod3/pureblood) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `dnspython`, `python-whois`, `requests`, `shodan`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python`
  - `wpscan`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[pureblood]] · [[BlackArch Tools]]

