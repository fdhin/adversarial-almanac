---
tool: "corscanner"
build-system: "scripts"
upstream: "https://github.com/chenjj/CORScanner"
recipe: "blackarch"
source: "https://github.com/chenjj/CORScanner"
license: "MIT"
blackarch-pkgver: "1.0.1.r2.g593043f"
---

# Installing corscanner

> Fast CORS misconfiguration vulnerabilities scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/corscanner) (version `1.0.1.r2.g593043f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S corscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/chenjj/CORScanner.git
cd CORScanner
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `future`, `gevent`, `requests`, `requests-file`, `tldextract`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[corscanner]] · [[BlackArch Tools]]

