---
tool: "machinae"
build-system: "scripts"
upstream: "https://github.com/HurricaneLabs/machinae"
recipe: "blackarch"
source: "https://github.com/HurricaneLabs/machinae"
license: "MIT"
blackarch-pkgver: "200.d5deae8"
---

# Installing machinae

> A tool for collecting intelligence from public sites/feeds about various security-related pieces of data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/machinae) (version `200.d5deae8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S machinae    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/HurricaneLabs/machinae) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `dateutil`, `defang`, `dnspython`, `feedparser`, `filemagic`, `html5lib`, `ipwhois`, `magic`, `pytz`, `relatime`, `requests`, `setuptools`, `stopit`, `tzlocal`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[machinae]] · [[BlackArch Tools]]

