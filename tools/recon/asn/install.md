---
tool: "asn"
build-system: "scripts"
upstream: "https://github.com/nitefood/asn"
recipe: "blackarch"
nix-package: "asn"
source: "https://github.com/nitefood/asn"
license: "MIT"
blackarch-pkgver: "0.78.0"
---

# Installing asn

> ASN, RPKI validity, BGP stats, IPv4v6, Prefix, URL, ASPath, Organization, IP reputation, IP geolocation, IP fingerprinting, Network recon, lookup API server, Web traceroute server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/asn) (version `0.78.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S asn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nitefood/asn) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `aha`
  - `bind`
  - `curl`
  - `grepcidr`
  - `ipcalc`
  - `jq`
  - `mtr`
  - `nmap`
  - `openbsd-netcat`
  - `whois`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[asn]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#asn    # ad-hoc run, no install
nix profile install nixpkgs#asn   # or persist it
```
