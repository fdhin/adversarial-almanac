---
tool: "icmpsh"
build-system: "perl"
upstream: "https://github.com/inquisb/icmpsh"
recipe: "blackarch"
source: "https://github.com/inquisb/icmpsh"
license: "custom:unknown"
blackarch-pkgver: "12.82caf34"
provides: ["icmpsh-m", "icmpsh-m-pl", "icmpsh-m-py"]
---

# Installing icmpsh

> Simple reverse ICMP shell.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/icmpsh) (version `12.82caf34`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S icmpsh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/inquisb/icmpsh) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/icmpsh-m`, `/usr/bin/icmpsh-m-pl`, `/usr/bin/icmpsh-m-py`

Backlinks: [[backdoor]] · [[icmpsh]] · [[BlackArch Tools]]

