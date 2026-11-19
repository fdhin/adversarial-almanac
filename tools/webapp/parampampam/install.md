---
tool: "parampampam"
build-system: "scripts"
upstream: "https://github.com/Bo0oM/ParamPamPam"
recipe: "blackarch"
source: "https://github.com/Bo0oM/ParamPamPam"
license: "custom:unknown"
blackarch-pkgver: "45.9171018"
---

# Installing parampampam

> This tool for brute discover GET and POST parameters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/parampampam) (version `45.9171018`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S parampampam    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Bo0oM/ParamPamPam.git
cd ParamPamPam
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `esprima`, `fuzzywuzzy`, `html5lib`, `levenshtein`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[parampampam]] · [[BlackArch Tools]]

