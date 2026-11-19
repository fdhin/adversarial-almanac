---
tool: "brute-force"
build-system: "scripts"
upstream: "https://github.com/Matrix07ksa/Brute_Force"
recipe: "blackarch"
source: "https://github.com/Matrix07ksa/Brute_Force"
license: "GPL3"
blackarch-pkgver: "52.78d1d8e"
---

# Installing brute-force

> Brute-Force attack tool for Gmail Hotmail Twitter Facebook Netflix.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brute-force) (version `52.78d1d8e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S brute-force    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Matrix07ksa/Brute_Force.git
cd Brute_Force
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `mechanize`, `proxylist`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[brute-force]] · [[BlackArch Tools]]

