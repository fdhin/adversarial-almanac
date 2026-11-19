---
tool: "cryptonark"
build-system: "perl"
upstream: "http://blog.techstacks.com/cryptonark.html"
recipe: "blackarch"
aur-package: "cryptonark"
source: "http://blog.techstacks.com/cryptonark.html"
license: "GPL-1.0-only"
blackarch-pkgver: "0.5.7"
provides: ["cnark"]
---

# Installing cryptonark

> SSL security checker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cryptonark) (version `0.5.7`, license GPL-1.0-only).

## Arch / BlackArch

```bash
sudo pacman -S cryptonark    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://blog.techstacks.com/cryptonark.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl-io-socket-ssl`
  - `perl-modern-perl`
  - `perl-mozilla-ca`
  - `perl-tie-hash-indexed`

## Installed layout (per recipe)

- Commands: `/usr/bin/cnark`

Backlinks: [[crypto]] · [[cryptonark]] · [[BlackArch Tools]]

