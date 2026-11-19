---
tool: "htpwdscan"
build-system: "scripts"
upstream: "https://github.com/lijiejie/htpwdScan"
recipe: "blackarch"
source: "https://github.com/lijiejie/htpwdScan"
license: "custom:unknown"
blackarch-pkgver: "23.e995d6f"
---

# Installing htpwdscan

> A python HTTP weak pass scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/htpwdscan) (version `23.e995d6f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S htpwdscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lijiejie/htpwdScan.git
cd htpwdScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests-ntlm`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[htpwdscan]] · [[BlackArch Tools]]

