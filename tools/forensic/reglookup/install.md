---
tool: "reglookup"
build-system: "python"
upstream: "https://projects.sentinelchicken.org/reglookup"
recipe: "blackarch"
source: "https://projects.sentinelchicken.org/reglookup"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.0.1+svn296.3.r0.gbc96ca3"
kali-package: "reglookup"
kali-version: "1.0.1+svn296-3"
---

# Installing reglookup

> Command line utility for reading and querying Windows NT registries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/reglookup) (version `1.0.1+svn296.3.r0.gbc96ca3`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S reglookup    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install reglookup    # Kali package version 1.0.1+svn296-3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://projects.sentinelchicken.org/reglookup) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `setuptools`, `wheel`
- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `docbook-utils`
  - `docbook2x`
  - `python`
  - `scons`
  - `talloc`

## Exact build steps from the recipe

```bash
cd $pkgname

  scons
  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[reglookup]] · [[BlackArch Tools]]

