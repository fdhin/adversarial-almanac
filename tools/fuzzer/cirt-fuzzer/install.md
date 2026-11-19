---
tool: "cirt-fuzzer"
build-system: "perl"
upstream: "http://www.cirt.dk/"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/fuzzer/fuzzer-cirt.tgz"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing cirt-fuzzer

> A simple TCP/UDP protocol fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cirt-fuzzer) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S cirt-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://dl.packetstormsecurity.net/fuzzer/fuzzer-cirt.tgz
tar xf fuzzer-cirt.tgz
cd fuzzer-cirt
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-algorithm-generatesequence`
  - `perl-net-ssleay`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[cirt-fuzzer]] · [[BlackArch Tools]]

