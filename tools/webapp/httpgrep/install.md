---
tool: "httpgrep"
build-system: "scripts"
upstream: "https://nullsecurity.net/tools/scanner.html"
recipe: "blackarch"
source: "https://nullsecurity.net/tools/scanner.html"
license: "custom:unknown"
blackarch-pkgver: "4.8"
---

# Installing httpgrep

> Async HTTP(S) scanner that greps response bodies and headers for strings or regex across hosts, ports, CIDR/ranges and TLS-cert vhosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpgrep) (version `4.8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S httpgrep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://nullsecurity.net/tools/scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[httpgrep]] · [[BlackArch Tools]]

