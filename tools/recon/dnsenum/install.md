---
tool: "dnsenum"
build-system: "perl"
upstream: "https://github.com/fwaeytens/dnsenum/"
recipe: "blackarch"
nix-package: "dnsenum"
source: "https://github.com/fwaeytens/dnsenum/"
license: "GPL"
blackarch-pkgver: "1.2.4.2"
kali-package: "dnsenum"
kali-version: "1.3.2-1"
---

# Installing dnsenum

> Script that enumerates DNS information from a domain, attempts zone transfers, performs a brute force dictionary style attack, and then performs reverse look-ups on the results.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsenum) (version `1.2.4.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dnsenum    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dnsenum    # Kali package version 1.3.2-1
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/fwaeytens/dnsenum/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-dns`
  - `perl-net-ip`
  - `perl-net-netmask`
  - `perl-net-whois-ip`
  - `perl-string-random`
  - `perl-www-mechanize`
  - `perl-xml-writer`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[dnsenum]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dnsenum    # ad-hoc run, no install
nix profile install nixpkgs#dnsenum   # or persist it
```
