---
tool: "windows-prefetch-parser"
build-system: "python"
upstream: "https://github.com/PoorBillionaire/Windows-Prefetch-Parser"
recipe: "blackarch"
source: "https://github.com/PoorBillionaire/Windows-Prefetch-Parser"
license: "Apache"
blackarch-pkgver: "88.bc1fa58"
provides: ["prefetch", "prefetch.py"]
---

# Installing windows-prefetch-parser

> Parse Windows Prefetch files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/windows-prefetch-parser) (version `88.bc1fa58`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S windows-prefetch-parser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PoorBillionaire/Windows-Prefetch-Parser.git
cd Windows-Prefetch-Parser
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/prefetch`, `/usr/bin/prefetch.py`

Backlinks: [[forensic]] · [[windows-prefetch-parser]] · [[BlackArch Tools]]

