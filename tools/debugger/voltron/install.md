---
tool: "voltron"
build-system: "python"
upstream: "https://github.com/snare/voltron"
recipe: "blackarch"
source: "https://github.com/snare/voltron"
license: "MIT"
blackarch-pkgver: "v0.1.8.r0.gbe9e54b"
---

# Installing voltron

> UI for GDB, LLDB and Vivisect's VDB.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/voltron) (version `v0.1.8.r0.gbe9e54b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S voltron    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/snare/voltron) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `blessed`, `flask`, `flask-restful`, `mock`, `nose`, `pexpect`, `pygments`, `pysigset`, `requests`, `requests-unixsocket`, `scruffington`, `scruffy`, `setuptools`, `six`, `werkzeug`
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

Backlinks: [[debugger]] · [[voltron]] · [[BlackArch Tools]]

