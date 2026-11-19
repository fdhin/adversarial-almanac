---
name: "ntds-decode"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://packetstormsecurity.com/files/121543/NTDS-Hash-Decoder.b.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ntds-decode

> Offline dumper that extracts LM and NTLM hashes from a copied Active Directory database.

## What it's for

NTDS LM/NTLM Hash Dumper (by dietrich, insecurety.net, 2013) parses the Active Directory database offline: given the `NTDS.dit` file and the `SYSTEM` registry hive copied off a domain controller, it decodes the password hashes of domain accounts and prints them in pwdump format, ready for offline cracking. It runs on Windows with Administrator rights and, by default, skips machine accounts and inactive, locked or disabled accounts. It is a single-purpose extractor — it does not attack hashes itself; feed its output to a cracker. The BlackArch package ships the original Windows executable and README under `/usr/share/windows/ntds-decode/`.

## Typical use cases

### Dump account hashes from a copied database

Point the tool at the SYSTEM hive and NTDS.dit you collected from the domain controller.

```
ntds_decode.exe -s SYSTEM -d NTDS.dit
```

Outcome: prints one pwdump-format line per active user account — `user:rid:lm-hash:ntlm-hash:description:home-directory` — which can be piped straight into a cracking tool.

### Include machine and disabled accounts

Widen the dump to cover computers and inactive, locked or disabled accounts.

```
ntds_decode.exe -s SYSTEM -d NTDS.dit -m -i
```

Outcome: the same pwdump-format output, now also containing machine accounts (`-m`) and accounts that are inactive, locked or disabled (`-i`), both omitted by default.

*No corpus evidence — derived from the README shipped in the packaged upstream archive (ntds_decode.zip, inspected directly).*
