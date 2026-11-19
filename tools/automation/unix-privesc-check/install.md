---
tool: "unix-privesc-check"
build-system: "scripts"
upstream: "http://pentestmonkey.net/tools/audit/unix-privesc-check"
recipe: "blackarch"
nix-package: "unix-privesc-check"
aur-package: "unix-privesc-check"
source: "http://pentestmonkey.net/tools/audit/unix-privesc-check"
license: "GPL2"
blackarch-pkgver: "1.4"
kali-package: "unix-privesc-check"
kali-version: "1.6+git20170904.00e0d54-0kali1"
---

# Installing unix-privesc-check

> Tries to find misconfigurations that could allow local unprivilged users to escalate privileges to other users or to access local apps (e.g. databases).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unix-privesc-check) (version `1.4`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S unix-privesc-check    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install unix-privesc-check    # Kali package version 1.6+git20170904.00e0d54-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://pentestmonkey.net/tools/audit/unix-privesc-check) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[unix-privesc-check]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#unix-privesc-check    # ad-hoc run, no install
nix profile install nixpkgs#unix-privesc-check   # or persist it
```
