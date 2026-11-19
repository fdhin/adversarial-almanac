---
tool: "billcipher"
build-system: "scripts"
upstream: "https://github.com/GitHackTools/BillCipher"
recipe: "blackarch"
source: "https://github.com/GitHackTools/BillCipher"
license: "GPL-3.0-or-later"
blackarch-pkgver: "32.97fba59"
---

# Installing billcipher

> Information Gathering tool for a Website or IP address.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/billcipher) (version `32.97fba59`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S billcipher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/GitHackTools/BillCipher.git
cd BillCipher
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[billcipher]] · [[BlackArch Tools]]

