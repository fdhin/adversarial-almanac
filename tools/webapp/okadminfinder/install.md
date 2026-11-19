---
tool: "okadminfinder"
build-system: "scripts"
upstream: "https://github.com/mIcHyAmRaNe/okadminfinder3"
recipe: "blackarch"
source: "https://github.com/mIcHyAmRaNe/okadminfinder3"
license: "Apache-2.0"
blackarch-pkgver: "122.a165681"
---

# Installing okadminfinder

> Tool to find admin panels / admin login pages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/okadminfinder) (version `122.a165681`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S okadminfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mIcHyAmRaNe/okadminfinder3.git
cd okadminfinder3
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `httpx`, `pysocks`, `requests`, `socksio`, `tqdm`, `trio`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[okadminfinder]] · [[BlackArch Tools]]

