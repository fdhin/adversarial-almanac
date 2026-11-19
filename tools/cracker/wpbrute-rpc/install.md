---
tool: "wpbrute-rpc"
build-system: "scripts"
upstream: "https://github.com/zendoctor/wpbrute-rpc"
recipe: "blackarch"
source: "https://github.com/zendoctor/wpbrute-rpc"
license: "custom:unknown"
blackarch-pkgver: "3.e7d8145"
---

# Installing wpbrute-rpc

> Tool for amplified bruteforce attacks on wordpress based website via xmlrcp API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpbrute-rpc) (version `3.e7d8145`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wpbrute-rpc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/zendoctor/wpbrute-rpc) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[wpbrute-rpc]] · [[BlackArch Tools]]

