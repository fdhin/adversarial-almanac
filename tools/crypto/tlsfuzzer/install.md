---
tool: "tlsfuzzer"
build-system: "python"
upstream: "https://github.com/tomato42/tlsfuzzer"
recipe: "blackarch"
source: "https://github.com/tomato42/tlsfuzzer"
license: "GPL2"
blackarch-pkgver: "1961.5eebc44"
---

# Installing tlsfuzzer

> SSL and TLS protocol test suite and fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tlsfuzzer) (version `1961.5eebc44`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S tlsfuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/tomato42/tlsfuzzer) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `coverage`, `coveralls`, `ecdsa`, `pyaml`, `pylint`, `requests`, `setuptools`, `six`, `tlslite-ng`
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

Backlinks: [[crypto]] · [[tlsfuzzer]] · [[BlackArch Tools]]

