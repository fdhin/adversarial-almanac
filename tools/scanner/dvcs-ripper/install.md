---
tool: "dvcs-ripper"
build-system: "perl"
upstream: "https://github.com/kost/dvcs-ripper"
recipe: "blackarch"
source: "https://github.com/kost/dvcs-ripper"
license: "GPL2"
blackarch-pkgver: "54.2c1bbc6"
provides: ["hg-decode", "rip-bzr", "rip-cvs", "rip-git", "rip-hg", "rip-svn"]
---

# Installing dvcs-ripper

> Rip web accessible (distributed) version control systems: SVN/GIT/BZR/CVS/HG.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dvcs-ripper) (version `54.2c1bbc6`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dvcs-ripper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/kost/dvcs-ripper) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-getopt-long`
  - `perl-io-socket-ssl`
  - `perl-lwp-protocol-https`

## Installed layout (per recipe)

- Commands: `/usr/bin/hg-decode`, `/usr/bin/rip-bzr`, `/usr/bin/rip-cvs`, `/usr/bin/rip-git`, `/usr/bin/rip-hg`, `/usr/bin/rip-svn`

Backlinks: [[scanner]] · [[dvcs-ripper]] · [[BlackArch Tools]]

