---
tool: "easyfuzzer"
build-system: "perl"
upstream: "http://www.mh-sec.de/downloads.html.en"
recipe: "blackarch"
source: "http://www.mh-sec.de/downloads.html.en"
license: "custom:unknown"
blackarch-pkgver: "3.6"
---

# Installing easyfuzzer

> A flexible fuzzer, not only for web, has a CSV output for efficient output analysis (platform independent).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/easyfuzzer) (version `3.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S easyfuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.mh-sec.de/downloads.html.en) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-io-socket-ip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[easyfuzzer]] · [[BlackArch Tools]]

