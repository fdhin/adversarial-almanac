---
tool: "smtp-test"
build-system: "scripts"
upstream: "https://github.com/isaudits/smtp-test"
recipe: "blackarch"
source: "https://github.com/isaudits/smtp-test"
license: "custom:unknown"
blackarch-pkgver: "6.151f636"
---

# Installing smtp-test

> Automated testing of SMTP servers for penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smtp-test) (version `6.151f636`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S smtp-test    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/isaudits/smtp-test) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cutycapt`
  - `nmap`
  - `python2`
- ⚠️ Python 2 only deps: python2-configparser, python2-dnspython (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smtp-test]] · [[BlackArch Tools]]

