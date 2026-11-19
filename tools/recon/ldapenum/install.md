---
tool: "ldapenum"
build-system: "perl"
upstream: "https://gobag.googlecode.com/svn-history/r2/trunk/ldap/ldapenum/"
recipe: "blackarch"
source: "https://projects.nth-dimension.org.uk/subversion/local/trunk/perl/ldapenum.pl"
license: "GPL2"
blackarch-pkgver: "0.1"
---

# Installing ldapenum

> Enumerate domain controllers using LDAP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ldapenum) (version `0.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ldapenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://gobag.googlecode.com/svn-history/r2/trunk/ldap/ldapenum/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-ldap`
  - `perl-net-dns`
  - `perl-net-nslookup`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ldapenum]] · [[BlackArch Tools]]

