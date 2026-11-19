---
name: "impacket-ntlmrelayx"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-ntlmrelayx

> NTLM relay server — capture authentication on SMB/HTTP listeners and replay it against another service.

## What it's for

ntlmrelayx.py starts listener servers (SMB, HTTP, WCF, RAW, RPC, WINRM, MSSQL, RDP — each disableable with a `--no-*-server` flag) and, when a client authenticates to one of them, relays that authentication to the target given by `-t` instead of merely capturing the hash. This turns "a user will connect to me" into "that user's session now exists on the target": the classic pairing is coercing an authentication out of a host (coercer bugs, poisoned lookups) while relaying into LDAPS or the AD CS web enrollment. It succeeds only where the target does not require signing/sealing and the relayed account has rights there; it will not break NTLM signing or relay into a protocol that mandates it.

## Typical use cases

### Relay into LDAPS and modify AD objects

Coerced authentication relayed to the domain controller's LDAPS, with the default escalation actions disabled and one specific change requested.

```
impacket-ntlmrelayx -t ldaps://198.51.100.10 --http-port 8080 --no-dump --no-da --no-validate-privs --add-dns-record attacker 198.51.100.99
```

Outcome: prints "Servers started, waiting for connections"; the first client that authenticates to the HTTP listener on port 8080 is relayed to LDAPS, and a DNS record named `attacker` pointing at 198.51.100.99 is added via LDAP — `--no-dump`, `--no-da` and `--no-validate-privs` suppress the default dump/domain-admin attempts so only the requested modification happens.

### Grant RBCD rights on a relayed machine account

Set up resource-based constrained delegation by relaying a machine account's own authentication to LDAP.

```
impacket-ntlmrelayx -t ldap://198.51.100.10 -smb2support --delegate-access
```

Outcome: when a computer account authenticates against the listener, its `msDS-AllowedToActOnBehalfOfOtherIdentity` is set to delegate to the attacking machine account (created beforehand, e.g. with addcomputer, and named via `-machine-account`/`-machine-hashes`); the tool reports the SAM name of the machine it took over, which is then the S4U target.

### Relay into AD CS web enrollment (ESC8)

Enroll a certificate for whoever authenticates to the listener.

```
impacket-ntlmrelayx -t https://dc01.example.com/certsrv/certfnsh.asp --adcs
```

Outcome: HTTP-authenticating victims are relayed into the CA's certificate-enrollment pages; on success a `.pfx` for the victim user is saved, usable for certificate-based (PKINIT) authentication or pass-the-cert.

### Relay captured SMB auth to another host

Classic lateral relay onto a second machine's SMB.

```
impacket-ntlmrelayx -smb2support -t smb://198.51.100.20
```

Outcome: clients authenticating to the listener are relayed to 198.51.100.20; when the relayed user is a local admin there, ntlmrelayx dumps the local SAM hashes by default and can execute a command with `-c`.
