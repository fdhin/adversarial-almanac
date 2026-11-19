---
tool: "tcpcontrol-fuzzer"
build-system: "perl"
upstream: "https://www.ee.oulu.fi/research/ouspg/tcpcontrol-fuzzer"
recipe: "blackarch"
source: "https://www.ee.oulu.fi/research/ouspg/tcpcontrol-fuzzer"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing tcpcontrol-fuzzer

> 2^6 TCP control bit fuzzer (no ECN or CWR).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcpcontrol-fuzzer) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tcpcontrol-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://www.ee.oulu.fi/research/ouspg/tcpcontrol-fuzzer) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-rawip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[tcpcontrol-fuzzer]] · [[BlackArch Tools]]

