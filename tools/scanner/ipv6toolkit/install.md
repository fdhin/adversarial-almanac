---
tool: "ipv6toolkit"
build-system: "perl"
upstream: "http://www.si6networks.com/tools/ipv6toolkit/"
recipe: "blackarch"
aur-package: "ipv6toolkit"
source: "http://www.si6networks.com/tools/ipv6toolkit/"
license: "GPL"
blackarch-pkgver: "861.9ee80b6"
---

# Installing ipv6toolkit

> SI6 Networks' IPv6 Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipv6toolkit) (version `861.9ee80b6`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ipv6toolkit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.si6networks.com/tools/ipv6toolkit/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ipv6toolkit]] · [[BlackArch Tools]]

