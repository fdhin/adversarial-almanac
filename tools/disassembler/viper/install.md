---
tool: "viper"
build-system: "scripts"
upstream: "https://github.com/botherder/viper"
recipe: "blackarch"
source: "https://github.com/botherder/viper"
license: "custom"
blackarch-pkgver: "2099.fdd7855"
---

# Installing viper

> A Binary analysis framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/viper) (version `2099.fdd7855`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S viper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/botherder/viper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `bitstring`, `bottle`, `dateutil`, `django`, `django-extensions`, `dnspython`, `jsonschema`, `magic`, `olefile`, `oletools`, `pbkdf2`, `pefile`, `pip`, `prettytable`, `pyasn1`, `pycryptodome`, `pydeep`, `pyelftools`, `pyexiftool`, `pylzma`, `pymisp`, `pypdns`, `pype32`, `pypssl`, `r2pipe`, `rarfile`, `requests`, `requests-cache`, `scandir`, `setuptools`, `six`, `sphinx`, `sqlalchemy`, `ssdeep`, `terminaltables`, `whitenoise`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `ssdeep`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[viper]] · [[BlackArch Tools]]

