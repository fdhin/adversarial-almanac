---
tool: "jeopardize"
build-system: "scripts"
upstream: "https://github.com/utkusen/jeopardize"
recipe: "blackarch"
source: "https://github.com/utkusen/jeopardize"
license: "BSD"
blackarch-pkgver: "5.34f1d07"
---

# Installing jeopardize

> A low(zero) cost threat intelligence & response tool against phishing domains.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jeopardize) (version `5.34f1d07`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S jeopardize    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/utkusen/jeopardize) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `chardet`, `colorama`, `dateutil`, `dnspython`, `lxml`, `psutil`, `python-whois`, `pytz`, `requests`, `selenium`, `termcolor`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[jeopardize]] · [[BlackArch Tools]]

