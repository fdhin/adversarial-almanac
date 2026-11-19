---
tool: "nimrm"
build-system: "windows"
upstream: "https://github.com/blue0x1/nimrm"
recipe: "blackarch"
source: "https://github.com/blue0x1/nimrm.git"
license: "MIT"
blackarch-pkgver: "v1.2.0.r12.g322c670"
provides: ["nimrm"]
---

# Installing nimrm

> Native WinRM shell client with NTLM, Kerberos, file transfers, in-memory operations, and multi-session support.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nimrm) (version `v1.2.0.r12.g322c670`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S nimrm    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/blue0x1/nimrm
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `krb5`
  - `nim`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Installed layout (per recipe)

- Commands: `/usr/bin/nimrm`

Backlinks: [[windows]] · [[nimrm]] · [[BlackArch Tools]]

