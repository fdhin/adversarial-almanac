---
tool: "ip-https-tools"
build-system: "scripts"
upstream: "https://github.com/takeshixx/ip-https-tools"
recipe: "blackarch"
source: "https://github.com/takeshixx/ip-https-tools"
license: "unknown"
blackarch-pkgver: "7.170691f"
provides: ["ip-https-test"]
---

# Installing ip-https-tools

> Tools for the IP over HTTPS (IP-HTTPS) Tunneling Protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ip-https-tools) (version `7.170691f`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S ip-https-tools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/takeshixx/ip-https-tools) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/ip-https-test`

Backlinks: [[tunnel]] · [[ip-https-tools]] · [[BlackArch Tools]]

