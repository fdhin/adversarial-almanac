---
tool: "wmi-forensics"
build-system: "scripts"
upstream: "https://github.com/davidpany/WMI_Forensics"
recipe: "blackarch"
source: "https://github.com/davidpany/WMI_Forensics"
license: "custom:unknown"
blackarch-pkgver: "11.0ab08dc"
provides: ["CCM_RUA_Finder", "PyWMIPersistenceFinder"]
---

# Installing wmi-forensics

> Scripts used to find evidence in WMI repositories.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wmi-forensics) (version `11.0ab08dc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wmi-forensics    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/davidpany/WMI_Forensics.git
cd WMI_Forensics
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/CCM_RUA_Finder`, `/usr/bin/PyWMIPersistenceFinder`

Backlinks: [[forensic]] · [[wmi-forensics]] · [[BlackArch Tools]]

