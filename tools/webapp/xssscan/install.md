---
tool: "xssscan"
build-system: "other"
upstream: "https://github.com/gwroblew/detectXSSlib"
recipe: "blackarch"
source: "https://github.com/gwroblew/detectXSSlib"
license: "MS-PL"
blackarch-pkgver: "17.7f1ea90"
---

# Installing xssscan

> Command line tool for detection of XSS attacks in URLs. Based on ModSecurity rules from OWASP CRS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xssscan) (version `17.7f1ea90`, license MS-PL).

## Arch / BlackArch

```bash
sudo pacman -S xssscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/gwroblew/detectXSSlib.git
cd detectXSSlib
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Exact build steps from the recipe

```bash
cd "$pkgname/tools/$pkgname"

  g++ -Wno-multichar -I../../detectxsslib -o $pkgname "$pkgname.cpp" \
    ../../detectxsslib/detectxsslib.c
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xssscan]] · [[BlackArch Tools]]

