---
tool: "morxtraversal"
build-system: "perl"
upstream: "http://www.morxploit.com/tools/"
recipe: "blackarch"
source: "http://web.archive.org/web/20160112201050/http://www.morxploit.com:80/morxtools/morxtraversal.pl"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing morxtraversal

> Path Traversal checking tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/morxtraversal) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S morxtraversal    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.morxploit.com/tools/) (the recipe uses a versioned archive).

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

Backlinks: [[webapp]] · [[morxtraversal]] · [[BlackArch Tools]]

