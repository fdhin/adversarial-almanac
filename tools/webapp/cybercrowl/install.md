---
tool: "cybercrowl"
build-system: "scripts"
upstream: "https://github.com/chamli/CyberCrowl"
recipe: "blackarch"
source: "https://github.com/chamli/CyberCrowl"
license: "custom:unknown"
blackarch-pkgver: "111.f7cac52"
---

# Installing cybercrowl

> A Python Web path scanner tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cybercrowl) (version `111.f7cac52`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cybercrowl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/chamli/CyberCrowl.git
cd CyberCrowl
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-fake-useragent, python2-idna, python2-requests, python2-requests-file, python2-requests-ntlm, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[cybercrowl]] · [[BlackArch Tools]]

