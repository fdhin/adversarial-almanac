---
tool: "rdp-sec-check"
build-system: "perl"
upstream: "https://github.com/portcullislabs/rdp-sec-check"
recipe: "blackarch"
source: "https://github.com/portcullislabs/rdp-sec-check"
license: "GPL2"
blackarch-pkgver: "11.d0cc143"
---

# Installing rdp-sec-check

> Script to enumerate security settings of an RDP Service.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rdp-sec-check) (version `11.d0cc143`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S rdp-sec-check    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/portcullislabs/rdp-sec-check) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-encoding-ber`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[rdp-sec-check]] · [[BlackArch Tools]]

