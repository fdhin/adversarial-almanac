---
tool: "krbrelayx"
build-system: "scripts"
upstream: "https://github.com/dirkjanm/krbrelayx"
recipe: "blackarch"
source: "https://github.com/dirkjanm/krbrelayx"
license: "MIT"
blackarch-pkgver: "72.dd22943"
---

# Installing krbrelayx

> Kerberos relaying and unconstrained delegation abuse toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/krbrelayx) (version `72.dd22943`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S krbrelayx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dirkjanm/krbrelayx) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `ldap3`, `ldapdomaindump`, `pyasn1`, `pycryptodome`, `pycryptodomex`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[krbrelayx]] · [[BlackArch Tools]]

