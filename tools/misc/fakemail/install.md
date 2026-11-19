---
tool: "fakemail"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/fakemail/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/fakemail/"
license: "GPL"
blackarch-pkgver: "1.0"
provides: ["fakemail.py"]
---

# Installing fakemail

> Fake mail server that captures e-mails as files for acceptance testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fakemail) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S fakemail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/fakemail/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/fakemail.py`

Backlinks: [[misc]] · [[fakemail]] · [[BlackArch Tools]]

