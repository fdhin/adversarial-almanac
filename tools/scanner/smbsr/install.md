---
tool: "smbsr"
build-system: "scripts"
upstream: "https://github.com/oldboy21/SMBSR"
recipe: "blackarch"
source: "https://github.com/oldboy21/SMBSR"
license: "Apache"
blackarch-pkgver: "50.7f86241"
---

# Installing smbsr

> Lookup for interesting stuff in SMB shares.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smbsr) (version `50.7f86241`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S smbsr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/oldboy21/SMBSR.git
cd SMBSR
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap`, `ldap3`, `pysmb`, `python-masscan`, `textract`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smbsr]] · [[BlackArch Tools]]

