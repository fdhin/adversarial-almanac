---
tool: "grammarinator"
build-system: "python"
upstream: "https://github.com/renatahodovan/grammarinator"
recipe: "blackarch"
source: "https://github.com/renatahodovan/grammarinator"
license: "MIT"
blackarch-pkgver: "523.46322fd"
---

# Installing grammarinator

> A random test generator / fuzzer that creates test cases according to an input ANTLR v4 grammar.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/grammarinator) (version `523.46322fd`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S grammarinator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/renatahodovan/grammarinator) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `antlerinator`, `antlr4`, `build`, `jinja`, `pip`, `wheel`
- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `autopep8`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[grammarinator]] · [[BlackArch Tools]]

