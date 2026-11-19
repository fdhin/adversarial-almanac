---
tool: "heartbleed-honeypot"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/126068/hb_honeypot.pl.txt"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/126068/hb_honeypot.pl.txt"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing heartbleed-honeypot

> Script that listens on TCP port 443 and responds with completely bogus SSL heartbeat responses, unless it detects the start of a byte pattern similar to that used in Jared Stafford's

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/heartbleed-honeypot) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S heartbleed-honeypot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/126068/hb_honeypot.pl.txt) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[heartbleed-honeypot]] · [[BlackArch Tools]]

