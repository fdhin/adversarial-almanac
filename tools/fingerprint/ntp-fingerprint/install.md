---
tool: "ntp-fingerprint"
build-system: "perl"
upstream: "http://www.hackingciscoexposed.com/?link=tools"
recipe: "blackarch"
source: "http://www.hackingciscoexposed.com/?link=tools"
license: "GPL2"
blackarch-pkgver: "0.1"
---

# Installing ntp-fingerprint

> An active fingerprinting utility specifically designed to identify the OS the NTP server is running on.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntp-fingerprint) (version `0.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ntp-fingerprint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.hackingciscoexposed.com/?link=tools) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[ntp-fingerprint]] · [[BlackArch Tools]]

