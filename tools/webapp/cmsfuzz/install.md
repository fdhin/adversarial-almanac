---
tool: "cmsfuzz"
build-system: "scripts"
upstream: "https://github.com/nahamsec/CMSFuzz"
recipe: "blackarch"
source: "https://github.com/nahamsec/CMSFuzz"
license: "GPL3"
blackarch-pkgver: "5.6be5a98"
---

# Installing cmsfuzz

> Fuzzer for wordpress, cold fusion, drupal, joomla, and phpnuke.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cmsfuzz) (version `5.6be5a98`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cmsfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nahamsec/CMSFuzz.git
cd CMSFuzz
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[cmsfuzz]] · [[BlackArch Tools]]

