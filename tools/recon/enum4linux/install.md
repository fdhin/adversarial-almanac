---
tool: "enum4linux"
build-system: "perl"
upstream: "http://labs.portcullis.co.uk/application/enum4linux/"
recipe: "blackarch"
nix-package: "enum4linux"
aur-package: "enum4linux"
source: "http://labs.portcullis.co.uk/application/enum4linux/"
license: "GPL"
blackarch-pkgver: "0.9.1"
kali-package: "enum4linux"
kali-version: "0.9.1-0kali2"
---

# Installing enum4linux

> A tool for enumerating information from Windows and Samba systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enum4linux) (version `0.9.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S enum4linux    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install enum4linux    # Kali package version 0.9.1-0kali2
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://labs.portcullis.co.uk/application/enum4linux/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openldap`
  - `perl`
  - `polenum`
  - `smbclient`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[enum4linux]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#enum4linux    # ad-hoc run, no install
nix profile install nixpkgs#enum4linux   # or persist it
```
