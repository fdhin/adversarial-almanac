---
tool: "striker"
build-system: "scripts"
upstream: "https://github.com/UltimateHackers/Striker"
recipe: "blackarch"
source: "https://github.com/UltimateHackers/Striker"
license: "GPL3"
blackarch-pkgver: "85.87c184d"
---

# Installing striker

> An offensive information and vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/striker) (version `85.87c184d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S striker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/UltimateHackers/Striker.git
cd Striker
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `ipy`, `mechanize`, `python-whois`, `requests`, `shodan`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[striker]] · [[BlackArch Tools]]

