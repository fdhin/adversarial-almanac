---
tool: "eternal-scanner"
build-system: "scripts"
upstream: "https://github.com/peterpt/eternal_scanner"
recipe: "blackarch"
source: "https://github.com/peterpt/eternal_scanner"
license: "MIT"
blackarch-pkgver: "101.3979b0f"
---

# Installing eternal-scanner

> An internet scanner for exploit CVE-0144 (Eternal Blue).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eternal-scanner) (version `101.3979b0f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S eternal-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/peterpt/eternal_scanner.git
cd eternal_scanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `masscan`
  - `metasploit`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[eternal-scanner]] · [[BlackArch Tools]]

