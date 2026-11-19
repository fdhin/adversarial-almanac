---
tool: "slowloris6"
ebuild-package: "slowloris6"
build-system: "perl"
upstream: "http://ha.ckers.org/slowloris/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/slowloris6"
version: "0.7.1"
license: "custom:unknown"
---

# Installing slowloris6

> IPv6 version - Slowloris HTTP DoS is a tool which is written in perl to test http-server vulnerabilites for connection exhaustion denial of service (DoS) attacks so you can enhance the security of your webserver.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/slowloris6) (version `0.7.1`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S slowloris6
```

The same PKGBUILD is usually on the AUR (`paru -S slowloris6`).

## Any other Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://ha.ckers.org/slowloris/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl-io-socket-inet6`
  - `perl-io-socket-ssl`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[dos]] · [[slowloris6]] · [[ArchStrike Gap Analysis]]
