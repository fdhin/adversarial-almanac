---
tool: "yawast"
build-system: "python"
upstream: "https://pypi.org/project/yawast-ng/#files"
recipe: "blackarch"
source: "https://pypi.org/project/yawast-ng/#files"
license: "BSD"
blackarch-pkgver: "0.12.1"
---

# Installing yawast

> The YAWAST Antecedent Web Application Security Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yawast) (version `0.12.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S yawast    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/yawast-ng/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `appdirs`, `asn1crypto`, `attrs`, `beautifulsoup4`, `build`, `cached-property`, `cerberus`, `certifi`, `cffi`, `chardet`, `colorama`, `cryptography`, `dateutil`, `distlib`, `dnspython`, `first`, `idna`, `importlib-metadata`, `jsonschema`, `more-itertools`, `nassl`, `ordered-set`, `orderedmultidict`, `packaging`, `pep517`, `pip`, `pip-shims`, `plette`, `psutil`, `publicsuffixlist`, `pycparser`, `pyparsing`, `requests`, `requests-mock`, `requirementslib`, `selenium`, `six`, `soupsieve`, `tls-parser`, `toml`, `tomlkit`, `urllib3`, `validator-collection`, `vistir`, `webdriver-manager`, `wheel`, `zipp`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `sslyze`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[yawast]] · [[BlackArch Tools]]

