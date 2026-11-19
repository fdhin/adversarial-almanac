---
tool: "webxploiter"
build-system: "scripts"
upstream: "https://github.com/xionsec/WebXploiter"
recipe: "blackarch"
source: "https://github.com/xionsec/WebXploiter"
license: "GPL3"
blackarch-pkgver: "56.c03fe6b"
---

# Installing webxploiter

> An OWASP Top 10 Security scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webxploiter) (version `56.c03fe6b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S webxploiter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/xionsec/WebXploiter.git
cd WebXploiter
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ndg-httpsclient`, `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ghost-py`
  - `libffi`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[webxploiter]] · [[BlackArch Tools]]

