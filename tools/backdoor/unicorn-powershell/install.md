---
tool: "unicorn-powershell"
build-system: "scripts"
upstream: "https://github.com/trustedsec/unicorn"
recipe: "blackarch"
source: "https://github.com/trustedsec/unicorn"
license: "custom:unknown"
blackarch-pkgver: "221.c1d914d"
---

# Installing unicorn-powershell

> A simple tool for using a PowerShell downgrade attack and inject shellcode straight into memory.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unicorn-powershell) (version `221.c1d914d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S unicorn-powershell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/trustedsec/unicorn.git
cd unicorn
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[unicorn-powershell]] · [[BlackArch Tools]]

