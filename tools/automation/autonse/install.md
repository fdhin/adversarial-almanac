---
tool: "autonse"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/AutoNSE"
recipe: "blackarch"
source: "https://github.com/m4ll0k/AutoNSE"
license: "GPL3"
blackarch-pkgver: "25.7c87f4c"
---

# Installing autonse

> Massive NSE (Nmap Scripting Engine) AutoSploit and AutoScanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/autonse) (version `25.7c87f4c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S autonse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m4ll0k/AutoNSE.git
cd AutoNSE
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `iputils`
  - `nmap`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[autonse]] · [[BlackArch Tools]]

