---
tool: "metafinder"
build-system: "python"
upstream: "https://github.com/Josue87/MetaFinder"
recipe: "blackarch"
source: "https://github.com/Josue87/MetaFinder"
license: "GPL3"
blackarch-pkgver: "v1.2.r2.g30c8475"
---

# Installing metafinder

> Search for documents in a domain through Search Engines (Google, Bing and Baidu). The objective is to extract metadata.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metafinder) (version `v1.2.r2.g30c8475`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S metafinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Josue87/MetaFinder.git
cd MetaFinder
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `openpyxl`, `pikepdf`, `prompt_toolkit`, `python-docx`, `python-pptx`, `requests`, `setuptools`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[metafinder]] · [[BlackArch Tools]]

