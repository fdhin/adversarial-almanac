---
tool: "snmpcheck"
build-system: "scripts"
upstream: "http://www.nothink.org/perl/snmpcheck/"
recipe: "blackarch"
nix-package: "snmpcheck"
source: "http://www.nothink.org/perl/snmpcheck/"
license: "GPL"
blackarch-pkgver: "1.9"
kali-package: "snmpcheck"
kali-version: "1.9-0kali2"
---

# Installing snmpcheck

> A free open source utility to get information via SNMP protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snmpcheck) (version `1.9`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S snmpcheck    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install snmpcheck    # Kali package version 1.9-0kali2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.nothink.org/perl/snmpcheck/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-snmp`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[snmpcheck]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#snmpcheck    # ad-hoc run, no install
nix profile install nixpkgs#snmpcheck   # or persist it
```
