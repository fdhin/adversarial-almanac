---
tool: "corsy"
build-system: "scripts"
upstream: "https://github.com/s0md3v/Corsy"
recipe: "blackarch"
source: "https://github.com/s0md3v/Corsy"
license: "GPL3"
blackarch-pkgver: "69.2985ae2"
---

# Installing corsy

> CORS Misconfiguration Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/corsy) (version `69.2985ae2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S corsy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/s0md3v/Corsy.git
cd Corsy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `tld`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[corsy]] · [[BlackArch Tools]]

