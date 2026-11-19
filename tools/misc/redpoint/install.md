---
tool: "redpoint"
build-system: "scripts"
upstream: "https://github.com/digitalbond/Redpoint3"
recipe: "blackarch"
source: "https://github.com/digitalbond/Redpoint3"
license: "custom:unknown"
blackarch-pkgver: "123.23ef36b"
---

# Installing redpoint

> Digital Bond's ICS Enumeration Tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/redpoint) (version `123.23ef36b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S redpoint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/digitalbond/Redpoint.git
cd Redpoint
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `nmap`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/nmap`

Backlinks: [[misc]] · [[redpoint]] · [[BlackArch Tools]]

