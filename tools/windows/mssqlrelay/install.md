---
tool: "mssqlrelay"
build-system: "windows"
upstream: "https://github.com/CompassSecurity/mssqlrelay"
recipe: "blackarch"
source: "https://github.com/CompassSecurity/mssqlrelay"
license: "MIT"
blackarch-pkgver: "12.1e03c96"
---

# Installing mssqlrelay

> Microsoft SQL Relay is an offensive tool for auditing and abusing Microsoft SQL (MSSQL) services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mssqlrelay) (version `12.1e03c96`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mssqlrelay    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/CompassSecurity/mssqlrelay
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `asn1crypto`, `cryptography`, `dnspython`, `dsinternals`, `ldap3`, `pyasn1`, `pyopenssl`, `requests`, `requests-ntlm`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket-ba`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[mssqlrelay]] · [[BlackArch Tools]]

