---
tool: "gtfo"
build-system: "scripts"
upstream: "https://github.com/mzfr/gtfo"
recipe: "blackarch"
source: "https://github.com/mzfr/gtfo"
license: "GPL-3.0-or-later"
blackarch-pkgver: "17.873d862"
---

# Installing gtfo

> Search gtfobins and lolbas files from your terminal.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gtfo) (version `17.873d862`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S gtfo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mzfr/gtfo) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `lxml`, `pyaml`, `pyfiglet`, `requests`, `requests-cache`, `tabulate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[gtfo]] · [[BlackArch Tools]]

