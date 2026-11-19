---
tool: "regeorg"
build-system: "scripts"
upstream: "https://github.com/sensepost/reGeorg"
recipe: "blackarch"
source: "https://github.com/sensepost/reGeorg"
license: "custom:Creative Commons"
blackarch-pkgver: "30.1ca54c2"
---

# Installing regeorg

> The successor to reDuh, pwn a bastion webserver and create SOCKS proxies through the DMZ. Pivot and pwn.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/regeorg) (version `30.1ca54c2`, license custom:Creative Commons).

## Arch / BlackArch

```bash
sudo pacman -S regeorg    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/sensepost/reGeorg.git
cd reGeorg
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[regeorg]] · [[BlackArch Tools]]

