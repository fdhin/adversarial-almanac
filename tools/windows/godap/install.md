---
tool: "godap"
build-system: "go"
upstream: "https://github.com/Macmod/godap"
recipe: "upstream"
license: "see repository"
---

# Installing godap

> Interactive terminal UI for LDAP/AD enumeration and object editing — DACLs, ADIDNS, GPOs, Kerberos and certificate auth.

Source: hacktricks feed; upstream [`Macmod/godap`](https://github.com/Macmod/godap).

## Arch Linux (AUR)

```bash
yay -S godap-bin
```

The AUR package `godap-bin` tracks upstream releases (also available:
Homebrew `brew install godap`, and Alpine Linux community `godap`).

## Prebuilt / releases

Automated binaries for several platforms are on the
[releases page](https://github.com/Macmod/godap/releases).

## Build from source

```bash
git clone https://github.com/Macmod/godap
cd godap
go install .
```

Backlinks: [[windows]] · [[godap]]
