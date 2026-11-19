---
tool: "fpdns"
build-system: "perl"
upstream: "https://github.com/kirei/fpdns"
recipe: "blackarch"
source: "https://github.com/kirei/fpdns"
license: "BSD"
blackarch-pkgver: "109.655bfa7"
provides: ["vendor_perl"]
---

# Installing fpdns

> Program that remotely determines DNS server versions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fpdns) (version `109.655bfa7`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S fpdns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/kirei/fpdns) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-dns`

## Installed layout (per recipe)

- Commands: `/usr/bin/vendor_perl`

Backlinks: [[fingerprint]] · [[fpdns]] · [[BlackArch Tools]]

