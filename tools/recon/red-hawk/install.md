---
tool: "red-hawk"
build-system: "scripts"
upstream: "https://github.com/Tuhinshubhra/RED_HAWK"
recipe: "blackarch"
source: "https://github.com/Tuhinshubhra/RED_HAWK"
license: "MIT"
blackarch-pkgver: "36.fa54e23"
---

# Installing red-hawk

> All in one tool for Information Gathering, Vulnerability Scanning and Crawling.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/red-hawk) (version `36.fa54e23`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S red-hawk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Tuhinshubhra/RED_HAWK.git
cd RED_HAWK
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[red-hawk]] · [[BlackArch Tools]]

