---
tool: "dnscan"
build-system: "scripts"
upstream: "https://github.com/rbsec/dnscan"
recipe: "blackarch"
source: "https://github.com/rbsec/dnscan"
license: "GPL3"
blackarch-pkgver: "209.281721e"
---

# Installing dnscan

> A python wordlist-based DNS subdomain scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnscan) (version `209.281721e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dnscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rbsec/dnscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cryptography`, `dnspython`, `netaddr`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dnscan]] · [[BlackArch Tools]]

