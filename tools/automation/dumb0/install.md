---
tool: "dumb0"
build-system: "perl"
upstream: "https://github.com/0verl0ad/Dumb0"
recipe: "blackarch"
source: "https://github.com/0verl0ad/Dumb0"
license: "GPL2"
blackarch-pkgver: "19.1493e74"
---

# Installing dumb0

> A simple tool to dump users in popular forums and CMS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dumb0) (version `19.1493e74`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dumb0    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/0verl0ad/Dumb0
cd Dumb0
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-libwww`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[dumb0]] · [[BlackArch Tools]]

