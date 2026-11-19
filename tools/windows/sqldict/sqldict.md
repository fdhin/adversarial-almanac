---
name: "sqldict"
categories:
  - blackarch-windows
version: "2.1"
homepage: "http://ntsecurity.nu/toolbox/sqldict/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2018-11-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sqldict

> Windows GUI dictionary attack against Microsoft SQL Server accounts.

## What it's for

SQLdict (Arne Vidstrom, ntsecurity.nu, v2.1) performs dictionary attacks against SQL Server authentication: given a target server address, an account name and a password-list file, it tries each word over SQL authentication until one succeeds or the list is exhausted. It is deliberately minimal — no instance discovery, no data dumping, just password guessing against a known account (typically `sa` on a legacy instance). The author's page points at the classic public wordlist archives of the era for input files. The packaged artifact is a small native Windows GUI executable (PE32). The same test is done today with hydra or Metasploit modules, but sqldict remains a standalone single-purpose checker that runs against any unhardened SQL Server still accepting SQL authentication.

## Typical use cases

### Dictionary-attack the sa account of a SQL Server

```
sqldict.exe
```

Outcome: opens the GUI; enter the target server address (e.g. `198.51.100.10`), the account name to attack (`sa`) and select a password list file — the tool walks the list over SQL authentication and reports the word, if any, that authenticates successfully.

*No corpus evidence — derived from upstream documentation.*
