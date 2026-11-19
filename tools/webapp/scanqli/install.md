---
tool: "scanqli"
build-system: "scripts"
upstream: "https://github.com/bambish/ScanQLi"
recipe: "blackarch"
source: "https://github.com/bambish/ScanQLi"
license: "GPL3"
blackarch-pkgver: "26.40a028d"
---

# Installing scanqli

> SQLi scanner to detect SQL vulns.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scanqli) (version `26.40a028d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S scanqli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/bambish/ScanQLi.git
cd ScanQLi
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `lxml`, `numpy`, `optparse-pretty`, `requests`, `termcolor`, `urllib3`, `validators`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[scanqli]] · [[BlackArch Tools]]

