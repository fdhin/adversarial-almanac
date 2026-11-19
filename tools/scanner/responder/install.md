---
tool: "responder"
build-system: "scripts"
upstream: "https://github.com/lgandx/Responder"
recipe: "blackarch"
nix-package: "responder"
aur-package: "responder"
source: "https://github.com/lgandx/Responder"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v3.2.2.0.r8.g424fbe5"
kali-package: "responder"
kali-version: "3.2.2.0-0kali2"
---

# Installing responder

> A LLMNR and NBT-NS poisoner, with built-in HTTP/SMB/MSSQL/FTP/LDAP rogue authentication server supporting NTLMv1/NTLMv2/LMv2 (multirelay version).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/responder) (version `v3.2.2.0.r8.g424fbe5`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S responder    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install responder    # Kali package version 3.2.2.0-0kali2
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lgandx/Responder.git
cd Responder
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aioquic`, `cryptography`, `netifaces`, `pycryptodome`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `mingw-w64-gcc`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[responder]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#responder    # ad-hoc run, no install
nix profile install nixpkgs#responder   # or persist it
```
