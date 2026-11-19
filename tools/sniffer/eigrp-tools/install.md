---
tool: "eigrp-tools"
build-system: "perl"
upstream: "http://www.hackingciscoexposed.com/?link=tools"
recipe: "blackarch"
source: "http://www.hackingciscoexposed.com/?link=tools"
license: "GPL2"
blackarch-pkgver: "0.1"
provides: ["eigrp"]
---

# Installing eigrp-tools

> This is a custom EIGRP packet generator and sniffer developed to test the security and overall operation quality of this brilliant Cisco routing protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eigrp-tools) (version `0.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S eigrp-tools    # needs the [blackarch] repo enabled
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
  - `perl-net-rawip`
  - `perl-netpacket`

## Installed layout (per recipe)

- Commands: `/usr/bin/eigrp`
- Data: `/usr/share/eigrp`

Backlinks: [[sniffer]] · [[eigrp-tools]] · [[BlackArch Tools]]

