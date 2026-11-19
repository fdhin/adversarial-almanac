---
tool: "chownat"
build-system: "perl"
upstream: "http://samy.pl/chownat/"
recipe: "blackarch"
source: "http://samy.pl/chownat/"
license: "custom:unknown"
blackarch-pkgver: "0.08b"
---

# Installing chownat

> Allows two peers behind two separate NATs with no port forwarding and no DMZ setup on their routers to directly communicate with each other

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chownat) (version `0.08b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S chownat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://samy.pl/chownat/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-digest-md4`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[chownat]] · [[BlackArch Tools]]

