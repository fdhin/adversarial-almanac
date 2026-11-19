---
tool: "socialfish"
build-system: "scripts"
upstream: "https://github.com/UndeadSec/SocialFish"
recipe: "blackarch"
source: "https://github.com/UndeadSec/SocialFish"
license: "BSD"
blackarch-pkgver: "266.3589ef3"
---

# Installing socialfish

> Ultimate phishing tool with Ngrok integrated.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/socialfish) (version `266.3589ef3`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S socialfish    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/UndeadSec/SocialFish.git
cd SocialFish
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `flask`, `flask-login`, `huepy`, `pylatex`, `python-nmap`, `qrcode`, `requests`, `wget`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[socialfish]] · [[BlackArch Tools]]

