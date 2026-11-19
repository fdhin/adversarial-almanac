---
tool: "glpwnme"
build-system: "python"
upstream: "https://github.com/Orange-Cyberdefense/glpwnme"
recipe: "blackarch"
source: "https://github.com/Orange-Cyberdefense/glpwnme"
license: "GPL-3.0-or-later"
blackarch-pkgver: "168.7101cd7"
---

# Installing glpwnme

> GLPI vulnerabilities checking tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/glpwnme) (version `168.7101cd7`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S glpwnme    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Orange-Cyberdefense/glpwnme) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `bs4`, `build`, `certifi`, `charset-normalizer`, `idna`, `installer`, `markdown-it-py`, `mdurl`, `packaging`, `poetry-core`, `pygments`, `requests`, `rich`, `setuptools`, `soupsieve`, `urllib3`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[glpwnme]] · [[BlackArch Tools]]

