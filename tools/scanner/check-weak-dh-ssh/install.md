---
tool: "check-weak-dh-ssh"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/66683/check_weak_dh_ssh.pl.bz2.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/check_weak_dh_ssh.pl.bz2"
license: "Apache"
blackarch-pkgver: "0.1"
---

# Installing check-weak-dh-ssh

> Debian OpenSSL weak client Diffie-Hellman Exchange checker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/check-weak-dh-ssh) (version `0.1`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S check-weak-dh-ssh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/66683/check_weak_dh_ssh.pl.bz2.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-data-dumper`
  - `perl-getopt-long`
  - `perl-math-bigint-gmp`
  - `perl-net-packet`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[check-weak-dh-ssh]] · [[BlackArch Tools]]

