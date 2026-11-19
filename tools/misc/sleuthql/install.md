---
tool: "sleuthql"
build-system: "scripts"
upstream: "https://github.com/RhinoSecurityLabs/SleuthQL"
recipe: "blackarch"
source: "https://github.com/RhinoSecurityLabs/SleuthQL"
license: "BSD"
blackarch-pkgver: "9.29fc878"
---

# Installing sleuthql

> Python3 Burp History parsing tool to discover potential SQL injection points. To be used in tandem with SQLmap.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sleuthql) (version `9.29fc878`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S sleuthql    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/RhinoSecurityLabs/SleuthQL.git
cd SleuthQL
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `lxml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[sleuthql]] · [[BlackArch Tools]]

