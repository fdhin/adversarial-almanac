---
tool: "snmp-fuzzer"
build-system: "perl"
upstream: "http://www.arhont.com/en/category/resources/tools-utilities/"
recipe: "blackarch"
source: "http://www.arhont.com/en/category/resources/tools-utilities/"
license: "custom"
blackarch-pkgver: "0.1.1"
---

# Installing snmp-fuzzer

> SNMP fuzzer uses Protos test cases with an entirely new engine written in Perl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snmp-fuzzer) (version `0.1.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S snmp-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.arhont.com/en/category/resources/tools-utilities/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[snmp-fuzzer]] · [[BlackArch Tools]]

