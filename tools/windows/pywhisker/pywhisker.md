---
name: "pywhisker"
categories:
  - windows
homepage: "https://github.com/ShutdownRepo/pywhisker"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: see repository
meta-checked: "2026-11-13"
github-repo: "ShutdownRepo/pywhisker"
github-match: "exact"
github-stars: 957
gh-checked: "2026-11-13"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# pywhisker

> Python tool for Shadow Credentials: add, remove and manage `msDS-KeyCredentialLink` entries on AD accounts to take them over with a certificate.

## What it's for

pyWhisker (ShutdownRepo) is the Python equivalent of Elad Shamir's C#
Whisker: it manipulates the `msDS-KeyCredentialLink` attribute of a target
user or computer in Active Directory. Write a key credential onto an
object you have write rights over (GenericAll/GenericWrite, etc.), and
that object can then authenticate via PKINIT with the generated
certificate — the "Shadow Credentials" takeover. It runs on Linux via
Impacket, supports password, pass-the-hash, Kerberos and
certificate-based (Schannel) authentication, and pairs with PKINITtools or
certipy to use the resulting PFX. Prerequisites are real: domain
functional level 2016+, a DC with its own certificate, and write access
to the target's `msDs-KeyCredentialLink` — without them the attack fails
(`KDC_ERR_PADATA_TYPE_NOSUPP`). See [[certipy]] for the all-in-one Python
alternative that covers this plus AD CS abuse.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### List existing key credentials

Check whether a target already carries key credentials before touching it.

```
python3 pywhisker.py -d corp.example.com -u user -p 'Password1' --target administrator --action list
```

Outcome: prints every KeyCredential on the target with its device-ID
(GUID) and creation time — the identifiers you need for `info` and
`remove` operations.

### Add a shadow credential (takeover)

Write your key onto a writable target and get authentication material.

```
python3 pywhisker.py -d corp.example.com -u user -p 'Password1' --target administrator --action add --filename shadow-admin
```

Outcome: generates a key credential, appends it to the target's
`msDS-KeyCredentialLink`, and writes `shadow-admin.pfx` plus a printed
password — authenticate as the target with PKINIT (certipy auth /
PKINITtools gettgthash) for a TGT in the target's name.

### Export the certificate as PEM

Emit PEM key/cert for tooling that wants them instead of PFX.

```
python3 pywhisker.py -d corp.example.com -u user -p 'Password1' --target administrator --action add --filename shadow-admin --export PEM
```

Outcome: same write as above but saves the material as PEM files
(`-P`/`--password` sets the PFX password when exporting PFX).

### Remove one or all key credentials

Clean up after takeover, or restore an attribute you only disturbed.

```
python3 pywhisker.py -d corp.example.com -u user -p 'Password1' --target administrator --action remove --device-id <guid>
python3 pywhisker.py -d corp.example.com -u user -p 'Password1' --target administrator --action clear
```

Outcome: `remove` deletes the single KeyCredential with the given
device-ID (from `list`); `clear` wipes every KeyCredential from the
target — destructive to legitimate Hello-for-Business keys if present, so
`list` first.

### Authenticate with a hash or Kerberos

Operate without plaintext credentials.

```
python3 pywhisker.py -d corp.example.com -u user --hashes :<nthash> --target administrator --action list
python3 pywhisker.py -d corp.example.com -u user -k --no-pass --dc-ip 198.51.100.10 --target administrator --action list
```

Outcome: the first uses NTLM pass-the-hash (`[LM:]NT` format); the second
uses Kerberos from the current `KRB5CCNAME` ccache (`-k --no-pass`),
pinned to a DC with `--dc-ip`. LDAPS is available with `--use-ldaps`, and
certificate/Schannel auth with `--use-schannel -crt cert.pem -key key.pem`.

### Spray shadow credentials across many targets

Mass-check write access with a target list.

```
python3 pywhisker.py -d corp.example.com -u user -p 'Password1' --target-list targets.txt --action spray
```

Outcome: attempts the `add` operation on every account named in
`targets.txt` (one per line), reporting per-target success/failure —
pairs naturally with permission-recon output.
