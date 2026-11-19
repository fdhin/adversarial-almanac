---
tool: "dex2jar"
build-system: "scripts"
upstream: "https://github.com/pxb1988/dex2jar"
recipe: "blackarch"
nix-package: "dex2jar"
aur-package: "dex2jar"
source: "https://github.com/pxb1988/dex2jar/files/1867564/dex-tools-2.1-SNAPSHOT.zip"
license: "Apache"
blackarch-pkgver: "2.4"
kali-package: "dex2jar"
kali-version: "2.1~nightly-28-0kali2"
---

# Installing dex2jar

> A tool for converting Android's .dex format to Java's .class format

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dex2jar) (version `2.4`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S dex2jar    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dex2jar    # Kali package version 2.1~nightly-28-0kali2
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/pxb1988/dex2jar/files/1867564/dex-tools-2.1-SNAPSHOT.zip
cd dex-tools-2.1-SNAPSHOT.zip
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[hardware]] · [[dex2jar]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dex2jar    # ad-hoc run, no install
nix profile install nixpkgs#dex2jar   # or persist it
```
