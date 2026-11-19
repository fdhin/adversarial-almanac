---
tool: "hoover"
build-system: "perl"
upstream: "https://github.com/xme/hoover/"
recipe: "blackarch"
source: "https://github.com/xme/hoover/"
license: "custom:unknown"
blackarch-pkgver: "4.9bda860"
---

# Installing hoover

> Wireless Probe Requests Sniffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hoover) (version `4.9bda860`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hoover    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/xme/hoover/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `net-tools`
  - `perl`
  - `perl-getopt-long`
  - `wireless_tools` — Debian wireless-tools / Fedora wireless-tools
  - `wireshark-cli`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[hoover]] · [[BlackArch Tools]]

