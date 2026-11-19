---
tool: "invoke-obfuscation"
build-system: "scripts"
upstream: "https://github.com/danielbohannon/Invoke-Obfuscation"
recipe: "blackarch"
source: "https://github.com/danielbohannon/Invoke-Obfuscation"
license: "Apache"
blackarch-pkgver: "45.f20e7f8"
---

# Installing invoke-obfuscation

> PowerShell Obfuscator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/invoke-obfuscation) (version `45.f20e7f8`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S invoke-obfuscation    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/danielbohannon/Invoke-Obfuscation.git
cd Invoke-Obfuscation
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[automation]] · [[invoke-obfuscation]] · [[BlackArch Tools]]

