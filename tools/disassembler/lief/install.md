---
tool: "lief"
build-system: "python"
upstream: "https://github.com/lief-project/lief"
recipe: "blackarch"
nix-package: "lief"
aur-package: "lief"
source: "https://github.com/lief-project/lief"
license: "Apache-2.0"
blackarch-pkgver: "0.17.3"
---

# Installing lief

> Library to instrument executable formats.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lief) (version `0.17.3`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S lief    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lief-project/lief) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `cattrs`, `installer`, `packaging`, `pathspec`, `pydantic`, `pydantic-core`, `pyproject-metadata`, `rich`, `scikit-build-core`, `setuptools`, `tomli`, `wheel`
- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `ninja`
  - `nlohmann-json`
  - `python`
  - `tl-expected`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  cmake \
    -B build \
    -G Ninja \
    -D CMAKE_BUILD_TYPE=Release \
    -D BUILD_SHARED_LIBS=ON \
    -D LIEF_EXAMPLES=OFF \
    -D LIEF_PYTHON_API=ON \
    -D LIEF_OPT_NLOHMANN_JSON_EXTERNAL=ON \
    -D LIEF_OPT_EXTERNAL_EXPECTED=ON
  cmake --build build

  cd api/python
  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[lief]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#lief    # ad-hoc run, no install
nix profile install nixpkgs#lief   # or persist it
```
