---
tool: "whatwaf"
build-system: "scripts"
upstream: "https://github.com/Ekultek/WhatWaf"
recipe: "blackarch"
source: "https://github.com/Ekultek/WhatWaf"
license: "BSD"
blackarch-pkgver: "398.06c815a"
---

# Installing whatwaf

> Detect and bypass web application firewalls and protection systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whatwaf) (version `398.06c815a`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S whatwaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ekultek/WhatWaf.git
cd WhatWaf
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[whatwaf]] · [[BlackArch Tools]]

