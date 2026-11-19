---
tool: "evilmaid"
build-system: "scripts"
upstream: "https://theinvisiblethings.blogspot.com"
recipe: "blackarch"
source: "https://theinvisiblethings.blogspot.com"
license: "custom:unknown"
blackarch-pkgver: "1.01"
---

# Installing evilmaid

> TrueCrypt loader backdoor to sniff volume password.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evilmaid) (version `1.01`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S evilmaid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://theinvisiblethings.blogspot.com) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[evilmaid]] · [[BlackArch Tools]]

