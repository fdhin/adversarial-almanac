---
tool: "phoneinfoga"
build-system: "scripts"
upstream: "https://github.com/sundowndev/PhoneInfoga"
recipe: "blackarch"
source: "https://github.com/sundowndev/PhoneInfoga"
license: "GPL3"
blackarch-pkgver: "v2.11.0.r6.g55807b0"
---

# Installing phoneinfoga

> Information gathering & OSINT framework for phone numbers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phoneinfoga) (version `v2.11.0.r6.g55807b0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S phoneinfoga    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/sundowndev/PhoneInfoga.git
cd PhoneInfoga
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[phoneinfoga]] · [[BlackArch Tools]]

