---
tool: "rsmangler"
build-system: "scripts"
upstream: "http://www.randomstorm.com/rsmangler-security-tool.php"
recipe: "blackarch"
nix-package: "rsmangler"
source: "http://www.randomstorm.com/rsmangler-security-tool.php"
license: "CCPL"
blackarch-pkgver: "1.4"
kali-package: "rsmangler"
kali-version: "1.5-0kali3"
---

# Installing rsmangler

> rsmangler takes a wordlist and mangle it

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rsmangler) (version `1.4`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S rsmangler    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install rsmangler    # Kali package version 1.5-0kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.randomstorm.com/rsmangler-security-tool.php) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[rsmangler]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rsmangler    # ad-hoc run, no install
nix profile install nixpkgs#rsmangler   # or persist it
```
