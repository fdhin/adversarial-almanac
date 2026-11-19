---
tool: "smbcrunch"
build-system: "perl"
upstream: "https://github.com/Raikia/SMBCrunch"
recipe: "blackarch"
source: "https://github.com/Raikia/SMBCrunch"
license: "GPL3"
blackarch-pkgver: "12.313400e"
provides: ["smbgrab", "smbhunt", "smblist"]
---

# Installing smbcrunch

> 3 tools that work together to simplify reconnaissance of Windows File Shares.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smbcrunch) (version `12.313400e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S smbcrunch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/Raikia/SMBCrunch.git
cd SMBCrunch
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- Commands: `/usr/bin/smbgrab`, `/usr/bin/smbhunt`, `/usr/bin/smblist`

Backlinks: [[recon]] · [[smbcrunch]] · [[BlackArch Tools]]

