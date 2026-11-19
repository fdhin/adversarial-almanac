---
tool: "proxycheck"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/61864/proxycheck.pl.txt.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/61864/proxycheck.pl.txt.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing proxycheck

> This is a simple proxy tool that checks for the HTTP CONNECT method and grabs verbose output from a webserver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/proxycheck) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S proxycheck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/61864/proxycheck.pl.txt.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-io-socket`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[proxycheck]] · [[BlackArch Tools]]

