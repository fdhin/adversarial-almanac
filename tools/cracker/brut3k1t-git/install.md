---
tool: "brut3k1t-git"
ebuild-package: "brut3k1t-git"
build-system: "python"
upstream: "https://github.com/ex0dus-0x/brute"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/brut3k1t-git"
version: "20210609.r104"
license: "GPL3"
---

# Installing brut3k1t-git

> Brute-force attack that supports multiple protocols and services

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/brut3k1t-git) (version `20210609.r104`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S brut3k1t-git
```

The same PKGBUILD is usually on the AUR (`paru -S brut3k1t-git`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ex0dus-0x/brute) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `appdirs`, `astroid`, `attrs`, `bcrypt`, `beautifulsoup4`, `black`, `certifi`, `cffi`, `chardet`, `click`, `cryptography`, `html5lib`, `idna`, `isort`, `lazy-object-proxy`, `mccabe`, `mechanize`, `mock`, `more-itertools`, `mypy_extensions`, `packaging`, `paramiko`, `pathspec`, `pluggy`, `py`, `pycparser`, `pylint`, `pynacl`, `pyparsing`, `pytest`, `regex`, `requests`, `selenium`, `six`, `soupsieve`, `toml`, `typed-ast`, `typing-extensions`, `urllib3`, `wcwidth`, `webencodings`, `wrapt`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mypy`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}"
  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[brut3k1t-git]] · [[ArchStrike Gap Analysis]]
