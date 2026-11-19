---
tool: "bypass-firewall-dns-history"
build-system: "scripts"
upstream: "https://github.com/vincentcox/bypass-firewalls-by-DNS-history"
recipe: "blackarch"
source: "https://github.com/vincentcox/bypass-firewalls-by-DNS-history"
license: "MIT"
blackarch-pkgver: "33.c55b7ce"
---

# Installing bypass-firewall-dns-history

> Firewall bypass script based on DNS history records.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bypass-firewall-dns-history) (version `33.c55b7ce`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S bypass-firewall-dns-history    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vincentcox/bypass-firewalls-by-DNS-history) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[bypass-firewall-dns-history]] · [[BlackArch Tools]]

