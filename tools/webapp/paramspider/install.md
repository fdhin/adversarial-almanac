---
tool: "paramspider"
build-system: "python"
upstream: "https://github.com/devanshbatham/ParamSpider"
recipe: "blackarch"
source: "https://github.com/devanshbatham/ParamSpider"
license: "MIT"
blackarch-pkgver: "v1.0.1.r3.gc44bdaa"
---

# Installing paramspider

> Mining URLs from dark corners of Web Archives for bug hunting/fuzzing/further probing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/paramspider) (version `v1.0.1.r3.gc44bdaa`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S paramspider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/devanshbatham/ParamSpider.git
cd ParamSpider
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `colorama`, `installer`, `requests`, `setuptools`, `wheel`
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

Backlinks: [[webapp]] · [[paramspider]] · [[BlackArch Tools]]

