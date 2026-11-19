---
tool: "slowloris"
build-system: "perl"
upstream: "http://ha.ckers.org/slowloris/"
recipe: "blackarch"
source: "http://ha.ckers.org/slowloris/"
license: "custom:unknown"
blackarch-pkgver: "0.7"
---

# Installing slowloris

> A tool which is written in perl to test http-server vulnerabilities for connection exhaustion denial of service (DoS) attacks so you can enhance the security of your webserver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/slowloris) (version `0.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S slowloris    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://ha.ckers.org/slowloris/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-io-socket-ssl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[slowloris]] · [[BlackArch Tools]]

