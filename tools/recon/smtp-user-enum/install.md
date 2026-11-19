---
tool: "smtp-user-enum"
build-system: "scripts"
upstream: "https://pentestmonkey.net/tools/user-enumeration/smtp-user-enum"
recipe: "blackarch"
nix-package: "smtp-user-enum"
source: "https://pentestmonkey.net/tools/user-enumeration/smtp-user-enum"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.2"
kali-package: "smtp-user-enum"
kali-version: "1.2-1kali4"
---

# Installing smtp-user-enum

> Username guessing tool primarily for use against the default Solaris SMTP service. Can use either EXPN, VRFY or RCPT TO.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smtp-user-enum) (version `1.2`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S smtp-user-enum    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install smtp-user-enum    # Kali package version 1.2-1kali4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pentestmonkey.net/tools/user-enumeration/smtp-user-enum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[smtp-user-enum]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#smtp-user-enum    # ad-hoc run, no install
nix profile install nixpkgs#smtp-user-enum   # or persist it
```
