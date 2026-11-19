---
tool: "sshatter"
build-system: "perl"
upstream: "http://www.nth-dimension.org.uk/downloads.php?id=34"
recipe: "blackarch"
source: "http://www.nth-dimension.org.uk/utils/get.php?downloadsid=34"
license: "GPL"
blackarch-pkgver: "1.2"
---

# Installing sshatter

> Password bruteforcer for SSH.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sshatter) (version `1.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sshatter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.nth-dimension.org.uk/downloads.php?id=34) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-cryptx`
  - `perl-net-ssh-perl`
  - `perl-parallel-forkmanager`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[sshatter]] · [[BlackArch Tools]]

