---
tool: "python-frida"
build-system: "python"
upstream: "https://pypi.org/project/frida/#files"
recipe: "blackarch"
aur-package: "python-frida"
source: "https://raw.githubusercontent.com/frida/frida/master/COPYING"
license: "custom:unknown"
blackarch-pkgver: "17.25.0"
---

# Installing python-frida

> Dynamic instrumentation toolkit for developers, reverse-engineers, and security researchers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-frida) (version `17.25.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S python-frida    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/frida/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `colorama`, `installer`, `prompt_toolkit`, `pygments`, `setuptools`, `tomlkit`, `typing_extensions`, `wheel`
- **Build tools**: `git`, `nodejs`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  if [[ -n $LDFLAGS ]]; then
    export LDFLAGS="${LDFLAGS//-Wl,-z,pack-relative-relocs/}"
  fi

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[python-frida]] · [[BlackArch Tools]]

