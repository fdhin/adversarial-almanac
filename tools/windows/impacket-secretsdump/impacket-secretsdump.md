---
name: "impacket-secretsdump"
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

# impacket-secretsdump

> Impacket's `secretsdump.py` (packaged on Kali as `impacket-secretsdump`): dumps hashes and secrets from a Windows host or domain — local SAM offline, LSA secrets, cached creds, and the whole NTDS.dit over the network.

## What it's for

One script covering every classic credential-extraction surface of a Windows machine or domain. In `LOCAL` mode it parses hive files you already copied off a host (`SAM`, `SYSTEM`, `SECURITY`, `NTDS.dit`) with zero network activity. Pointed at `[[domain/]username[:password]@]target` it authenticates over SMB/RPC — with a password, an NTLM hash (pass-the-hash), or a Kerberos ccache — reads SAM and LSA secrets from the registry, and pulls the AD database itself (`NTDS.dit`) by speaking DRSUAPI `DRSGetNCChanges` to a domain controller, i.e. a DCSync, without executing any agent on the target. Accounts with domain-replication rights (Domain Admin, or a DC machine account) get the full domain hash database in minutes. It is the per-script note for `examples/secretsdump.py` of Fortra's Impacket — the library and its other example tools are documented separately under `windows/impacket-*` and in [[impacket-ba]].

## Typical use cases

### Dump local account hashes offline from saved hives

No network involved: parse a `SAM` hive with its matching `SYSTEM` bootkey.

```
impacket-secretsdump -sam sam.save -system system.save LOCAL
```

Outcome: prints one `username:RID:LM:NT:::` line per local account (RID 500 = local administrator), plus the machine account if a `SECURITY` hive is supplied (`-security security.save`) along with LSA secrets and cached domain credentials. LOCAL is case-insensitive and no credentials are needed — this is the standard post-processing step after `reg save HKLM\SAM`, `reg save HKLM\SYSTEM`, `reg save HKLM\SECURITY` (or a volume-shadow copy) on the target. Add `-outputfile hashes` to write the same data to `hashes.sam` / `hashes.secrets` / `hashes.cached` files instead of stdout.

### Dump the AD database offline from a copied NTDS.dit

Decrypt the domain hash database from disk with the DC's SYSTEM hive.

```
impacket-secretsdump -system system.save -ntds ntds.dit LOCAL
```

Outcome: every domain account with its NT (and LM) hashes, Kerberos AES keys, password-history and account metadata, `user:RID:LM:NT:::` per line — thousands of lines on a real DC. This is what you do with an `ntds.dit` grabbed via vssadmin, ntdsutil, or a disk image; both files must come from the same DC, since the SYSTEM hive holds the decryption key (PEK). `-outputfile dump` splits results into `dump.ntds` and friends.

### Dump everything from a remote host with a password

Authenticate to a machine over SMB and pull all local secrets.

```
impacket-secretsdump 'corp.example.com/user:Password1!@198.51.100.10'
```

Outcome: the script enables the remote registry as needed, extracts SAM hashes, LSA secrets (service account plaintexts, DPAPI keys) and cached credentials from the target, and prints them to stdout. Against a domain controller with an account holding replication rights it additionally performs a DCSync and dumps the full NTDS database. The password can be left off (`corp.example.com/user@198.51.100.10`) to be prompted instead; use `-dc-ip` when the target name resolves differently from the DC you actually want.

### Dump from a remote host with only a hash (pass-the-hash)

Authenticate as an account whose NTLM hash you hold, no password required.

```
impacket-secretsdump -hashes :c4a9d2f7e1b80694a3d5f2c8b7e61049 corp.example.com/user@198.51.100.10
```

Outcome: full dump as with a password, authenticating via NTLM with the supplied hash (`-hashes LM:NT` — the LM half is conventionally left empty as shown). A DC machine account works the same way and is enough for the NTDS part, since machine accounts of domain controllers hold replication rights: `-hashes :<nt> 'corp.example.com/dc01$@198.51.100.10'` (quote the `$` in shells).

### Dump using a Kerberos ticket (pass-the-ticket over ccache)

Authenticate with a ccache ticket instead of password or hash — the natural follow-up to any ticket-forcing attack.

```
KRB5CCNAME=dc01.ccache impacket-secretsdump -k -no-pass dc01.corp.example.com
```

Outcome: `-k` switches authentication to Kerberos using the ticket in the `KRB5CCNAME` ccache, and `-no-pass` suppresses the password prompt that would otherwise appear; the target should be given as an FQDN (add `-dc-ip 198.51.100.10` when DNS is not reliable through your position). The dump itself is identical to password-based runs — commonly used with a forwarded/silver ticket for the DC to go straight to the NTDS data.

### Pull only the domain database, or only one user

Scope a DCSync to exactly what you need instead of the whole domain.

```
impacket-secretsdump -just-dc-user administrator 'corp.example.com/user:Password1!@198.51.100.10'
```

Outcome: `-just-dc-user administrator` extracts only that account's NTDS record (hashes and Kerberos keys) — far faster and quieter than the full dump, and the standard move when you only need `administrator` or `krbtgt`; it implies `-just-dc`, which restricts the run to NTDS data (NTLM hashes + Kerberos keys) skipping SAM/LSA/cached entirely. Add `-just-dc-ntlm` for NTLM hashes only. DRSUAPI-only: incompatible with `-use-vss`, `-resumefile` and `LOCAL` mode (the script errors out rather than guessing).

### Show which dumped accounts are still enabled

Flag disabled accounts in the output before you spend cracking time on them.

```
impacket-secretsdump -user-status -just-dc 'corp.example.com/dc01$@198.51.100.10' -hashes :c4a9d2f7e1b80694a3d5f2c8b7e61049
```

Outcome: each NTDS line gains an `Enabled`/`Disabled` status column (it decorates the dump rather than filtering it), so locked-out or historic-service accounts can be skipped when prioritizing cracking or spray targets. Combines with `-pwd-last-set` (staleness) and `-history` (password history hashes) for triage.

### Extract NTDS.dit via VSS with a chosen exec method

Fall back to copying the database itself when DRSUAPI is not an option.

```
impacket-secretsdump -use-vss -exec-method smbexec 'corp.example.com/user:Password1!@198.51.100.10'
```

Outcome: instead of DRSUAPI, the script executes `vssadmin` on the target to create a shadow copy, copies `ntds.dit` plus hives out of it, and parses them locally — `-exec-method` selects the remote-execution vehicle for that step (`smbexec` default, `wmiexec` and `mmcexec` as alternatives when SMB-based service creation is blocked). Slower and noisier than DRSUAPI (it touches the disk and event logs), but it works with plain admin rights on a DC and no replication privileges.

*Corpus evidence: command lines in the corpus index cover LOCAL SAM/SYSTEM/SECURITY dumps, LOCAL NTDS dumps, password, pass-the-hash and ccache authentication, and `-just-dc-user` runs; `-user-status` and `-use-vss`/`-exec-method` are verified against the current `examples/secretsdump.py` argparse of fortra/impacket. All flags above cross-checked against that source.*
