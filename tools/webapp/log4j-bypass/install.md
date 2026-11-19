---
tool: "log4j-bypass"
build-system: "scripts"
upstream: "https://github.com/cyberqueen-meg/log4j-bypass"
recipe: "blackarch"
source: "https://github.com/cyberqueen-meg/log4j-bypass"
license: "MIT"
blackarch-pkgver: "33.f5c92f9"
---

# Installing log4j-bypass

> Log4j web app tester that includes WAF bypasses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/log4j-bypass) (version `33.f5c92f9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S log4j-bypass    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cyberqueen-meg/log4j-bypass) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pycryptodome`, `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[log4j-bypass]] · [[BlackArch Tools]]

