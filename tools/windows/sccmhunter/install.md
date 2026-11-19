---
tool: "sccmhunter"
build-system: "windows"
upstream: "https://github.com/garrettfoster13/sccmhunter"
recipe: "blackarch"
nix-package: "sccmhunter"
source: "https://github.com/garrettfoster13/sccmhunter"
license: "MIT"
blackarch-pkgver: "v2.0.0.r79.ge32fe74"
---

# Installing sccmhunter

> Identifying, profiling, and attacking SCCM related assets in an Active Directory domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sccmhunter) (version `v2.0.0.r79.ge32fe74`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sccmhunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/garrettfoster13/sccmhunter
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `cmd2`, `cryptography`, `installer`, `ldap3`, `pandas`, `pyasn1`, `pyasn1-modules`, `pycryptodome`, `pyopenssl`, `requests`, `requests-ntlm`, `requests-toolbelt`, `rich`, `setuptools`, `tabulate`, `typer`, `urllib3`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket-ba`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[sccmhunter]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sccmhunter    # ad-hoc run, no install
nix profile install nixpkgs#sccmhunter   # or persist it
```
