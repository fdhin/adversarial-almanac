---
tool: "cisco-snmp-enumeration"
build-system: "scripts"
upstream: "https://github.com/nccgroup/cisco-snmp-enumeration"
recipe: "blackarch"
source: "https://github.com/nccgroup/cisco-snmp-enumeration"
license: "GPL3"
blackarch-pkgver: "10.ad06f57"
---

# Installing cisco-snmp-enumeration

> Automated Cisco SNMP Enumeration, Brute Force, Configuration Download and Password Cracking.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cisco-snmp-enumeration) (version `10.ad06f57`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cisco-snmp-enumeration    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/cisco-SNMP-enumeration.git
cd cisco-SNMP-enumeration
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `john`
  - `metasploit`
  - `net-snmp`
  - `screen`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[cisco-snmp-enumeration]] · [[BlackArch Tools]]

