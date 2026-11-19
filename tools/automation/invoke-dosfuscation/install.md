---
tool: "invoke-dosfuscation"
build-system: "scripts"
upstream: "https://github.com/danielbohannon/Invoke-DOSfuscation"
recipe: "blackarch"
source: "https://github.com/danielbohannon/Invoke-DOSfuscation"
license: "Apache"
blackarch-pkgver: "7.6260f5b"
---

# Installing invoke-dosfuscation

> Cmd.exe Command Obfuscation Generator & Detection Test Harness.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/invoke-dosfuscation) (version `7.6260f5b`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S invoke-dosfuscation    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/danielbohannon/Invoke-DOSfuscation
cd Invoke-DOSfuscation
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[automation]] · [[invoke-dosfuscation]] · [[BlackArch Tools]]

