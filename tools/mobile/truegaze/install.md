---
tool: "truegaze"
build-system: "python"
upstream: "https://github.com/nightwatchcybersecurity/truegaze"
recipe: "blackarch"
source: "https://github.com/nightwatchcybersecurity/truegaze"
license: "Apache"
blackarch-pkgver: "117.c3f26bc"
---

# Installing truegaze

> Static analysis tool for Android/iOS apps focusing on security issues outside the source code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/truegaze) (version `117.c3f26bc`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S truegaze    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nightwatchcybersecurity/truegaze) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `asn1crypto`, `beautifultable`, `click`, `cryptography`, `jsonschema`, `pytest`, `pytest-cov`, `requests-mock`, `roca-detect`, `setuptools`, `tldextract`, `toml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `androguard`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[truegaze]] · [[BlackArch Tools]]

