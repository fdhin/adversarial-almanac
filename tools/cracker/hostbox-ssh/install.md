---
tool: "hostbox-ssh"
build-system: "scripts"
upstream: "http://stridsmanit.wordpress.com/2012/12/02/brute-forcing-passwords-with-hostbox-ssh-0-1-1/"
recipe: "blackarch"
source: "http://stridsmanit.wordpress.com/2012/12/02/brute-forcing-passwords-with-hostbox-ssh-0-1-1/"
license: "GPL"
blackarch-pkgver: "0.1.1"
---

# Installing hostbox-ssh

> A ssh password/account scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hostbox-ssh) (version `0.1.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S hostbox-ssh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://stridsmanit.wordpress.com/2012/12/02/brute-forcing-passwords-with-hostbox-ssh-1-1/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `wxpython`
- ⚠️ Python 2 only deps: python2-paramiko (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[hostbox-ssh]] · [[BlackArch Tools]]

