---
tool: "argus"
build-system: "perl"
upstream: "http://qosient.com/argus/"
recipe: "blackarch"
nix-package: "argus"
aur-package: "argus"
source: "http://qosient.com/argus/"
license: "GPL3"
blackarch-pkgver: "3.0.8.2"
---

# Installing argus

> Network monitoring tool with flow control.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/argus) (version `3.0.8.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S argus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://qosient.com/argus/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libsasl`
  - `perl`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[argus]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#argus    # ad-hoc run, no install
nix profile install nixpkgs#argus   # or persist it
```
