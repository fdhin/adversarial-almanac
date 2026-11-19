---
tool: "angr"
build-system: "python"
upstream: "https://pypi.org/project/angr/#files"
recipe: "blackarch"
source: "https://pypi.org/project/angr/#files"
license: "BSD-2-Clause"
blackarch-pkgver: "9.2.205"
---

# Installing angr

> The next-generation binary analysis platform from UC Santa Barbaras Seclab.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/angr) (version `9.2.205`, license BSD-2-Clause).

## Arch / BlackArch

```bash
sudo pacman -S angr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/angr/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `ailment`, `ana`, `archinfo`, `bintrees`, `build`, `cachetools`, `capstone`, `cffi`, `claripy`, `cle`, `colorama`, `cooldict`, `cppheaderparser`, `cxxheaderparser`, `dpkt`, `gitpython`, `installer`, `itanium-demangler`, `lmdb`, `msgspec`, `mulpyplexer`, `nampa`, `networkx`, `progressbar`, `protobuf`, `psutil`, `pycparser`, `pydemumble`, `pyelftools`, `pyformlang`, `pyvex`, `rich`, `rpyc`, `setuptools`, `setuptools-rust`, `simuvex`, `sortedcontainers`, `sympy`, `unicorn`, `unique-log-filter`, `wheel`, `z3-solver`
- **Build tools**: `rust`
- **System libraries** (Arch name — translate for your distro):
  - `libffi`
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[angr]] · [[BlackArch Tools]]

