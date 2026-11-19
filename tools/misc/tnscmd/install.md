---
tool: "tnscmd"
build-system: "perl"
upstream: "http://www.jammed.com/~jwa/hacks/security/tnscmd/"
recipe: "blackarch"
source: "http://www.jammed.com/~jwa/hacks/security/tnscmd/"
license: "GPL"
blackarch-pkgver: "1.3"
---

# Installing tnscmd

> A lame tool to prod the oracle tnslsnr process (1521/tcp).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tnscmd) (version `1.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S tnscmd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.jammed.com/~jwa/hacks/security/tnscmd/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[tnscmd]] · [[BlackArch Tools]]

