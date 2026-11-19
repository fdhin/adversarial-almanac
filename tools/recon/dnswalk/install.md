---
tool: "dnswalk"
build-system: "perl"
upstream: "http://sourceforge.net/projects/dnswalk/"
recipe: "blackarch"
aur-package: "dnswalk"
source: "http://sourceforge.net/projects/dnswalk/"
license: "GPL"
blackarch-pkgver: "2.0.2"
kali-package: "dnswalk"
kali-version: "2.0.2.dfsg.1-3"
---

# Installing dnswalk

> A DNS debugger and zone-transfer utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnswalk) (version `2.0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dnswalk    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dnswalk    # Kali package version 2.0.2.dfsg.1-3
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/dnswalk/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl-net-dns`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[dnswalk]] · [[BlackArch Tools]]

