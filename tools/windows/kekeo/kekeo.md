---
name: "kekeo"
categories:
  - blackarch-windows
version: "2.2.0_20211214"
homepage: "https://github.com/gentilkiwi/kekeo"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-07"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "gentilkiwi/kekeo"
github-stars: 1528
github-pushed: "2021-12-14"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kekeo

> Standalone Kerberos toolbox by gentilkiwi — request, save, and inject Windows tickets from a single console.

## What it's for

kekeo (Benjamin Delpy, gentilkiwi) is a single-console Windows tool dedicated to Microsoft Kerberos: it requests tickets from a KDC using a password, an NT hash, a modern Kerberos key or a certificate, saves and reloads them as `.kirbi` files, and injects tickets into the current logon session. It is the classic standalone companion to mimikatz for ticket-based attacks — overpass-the-hash (turning an NT hash into a usable Kerberos TGT) and pass-the-ticket. It works with credentials you already hold; it does not dump secrets from memory like mimikatz. Run `kekeo.exe` and enter module commands at its interactive console (prefixed like `tgt::`, `kerberos::`); distributed binaries depend on a licensed ASN.1/C toolkit, so rebuilding from source needs an OSS ASN.1/C licence.

## Typical use cases

### Ask a TGT with a password

```
tgt::ask /user:user /domain:example.com /password:Password1
```

Outcome: performs the AS-REQ against the domain KDC and writes the TGT to `TGT_user@EXAMPLE.COM.kirbi` in the working directory.

### Ask a TGT with an NT hash (overpass-the-hash)

```
tgt::ask /user:user /domain:example.com /rc4:0123456789abcdef0123456789abcdef
```

Outcome: uses the RC4/NT key directly instead of a password and writes the same TGT file; `/aes256:` and `/aes128:` accept modern Kerberos encryption keys instead.

### Inject a ticket into the current session (pass-the-ticket)

```
kerberos::ptt TGT_user@EXAMPLE.COM.kirbi
```

Outcome: submits the `.kirbi` ticket to the local LSA for the current logon session, no account key needed; new Kerberos connections then use the injected ticket. A wildcard injects every ticket in the working directory (`kerberos::ptt *.kirbi`).

### Ask and inject in one step

```
tgt::ask /user:user /domain:example.com /password:Password1 /ptt
```

Outcome: same AS-REQ, but the TGT goes straight into the current session instead of being written to disk.

*No corpus evidence — derived from upstream documentation and module sources.*
