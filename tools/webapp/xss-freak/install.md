---
tool: "xss-freak"
build-system: "scripts"
upstream: "https://github.com/hacker900123/XSS-Freak"
recipe: "blackarch"
source: "https://github.com/hacker900123/XSS-Freak"
license: "custom:unknown"
blackarch-pkgver: "17.e361766"
---

# Installing xss-freak

> An XSS scanner fully written in Python3 from scratch.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xss-freak) (version `17.e361766`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xss-freak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hacker900123/XSS-Freak.git
cd XSS-Freak
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `asciistuff`, `beautifulsoup4`, `colorama`, `requests`, `user-agent`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xss-freak]] · [[BlackArch Tools]]

