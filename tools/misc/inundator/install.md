---
tool: "inundator"
build-system: "perl"
upstream: "http://inundator.sourceforge.net/"
recipe: "blackarch"
source: "http://inundator.sourceforge.net/"
license: "BSD"
blackarch-pkgver: "0.5"
---

# Installing inundator

> An ids evasion tool, used to anonymously inundate intrusion detection logs with false positives in order to obfuscate a real attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/inundator) (version `0.5`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S inundator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://inundator.sourceforge.net/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `nmap`
  - `perl`
  - `perl-net-cidr`
  - `perl-net-socks`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[inundator]] · [[BlackArch Tools]]

