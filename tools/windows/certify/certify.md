---
name: "certify"
categories:
  - windows
homepage: "https://github.com/GhostPack/Certify"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
license: see repository
meta-checked: "2026-11-13"
github-repo: "GhostPack/Certify"
github-match: "exact"
github-stars: 2052
gh-checked: "2026-11-13"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# certify

> Enumerate and abuse Active Directory Certificate Services (AD CS) misconfigurations — ESC1–ESC16 discovery, certificate requests, and CA/template manipulation.

## What it's for

Certify (GhostPack, harmj0y/tifkin_) is the reference C# tool for attacking
Active Directory Certificate Services: it enumerates enterprise CAs and
certificate templates, flags the misconfigurations behind the ESC1–ESC16
attack classes (e.g. enrollee-supplies-SAN templates writable by low-
privileged users), requests certificates that authenticate as arbitrary
principals, and can manage or reconfigure CAs and templates when you hold
the rights. Reach for it on any domain engagement once you have a domain
account — a vulnerable template plus a client-auth EKU yields a
certificate usable for PKINIT/NTLM as the target user. The current 2.x CLI
uses subcommand-style verbs (`enum-cas`, `enum-templates`, `request`,
`forge`, ...); the older 1.x verbs that most published guidance still
shows (`find /vulnerable`, `cas`, `request /ca:...`) were renamed and no
longer work in current builds. Related vault notes: [[certipy]] (the Python equivalent),
[[certi]], and [[ghostpack]] for precompiled suite binaries.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Hunt vulnerable certificate templates

The first command of every AD CS review.

```
Certify.exe enum-templates --filter-enabled --filter-vulnerable --hide-admins
```

Outcome: lists only templates published by a CA and marked vulnerable,
vulnerability-class per template (ESC1–...), with enrollment permissions
trimmed of built-in admin noise. Default vulnerability marking assumes the
built-in low-priv groups; pass `--current-user` or `--target-user <user>`
to judge exploitability for a specific principal.

### Enumerate enterprise CAs

Map the PKI before touching templates.

```
Certify.exe enum-cas
```

Outcome: every CA in the forest with its security descriptor, web
enrollment status and enabled templates; `--filter-vulnerable` keeps only
CAs marked vulnerable for your principal.

### Request a certificate as another user (ESC1-style)

Turn an enrollee-supplies-SAN template into authentication material.

```
Certify.exe request --ca "dc01.corp.example.com\corp-DC01-CA" --template CorpUser --upn administrator@corp.example.com
```

Outcome: enrolls against the named CA/template, supplies the target UPN as
SAN, and prints a base64 PFX with its private-key password — save it and
authenticate with PKINIT tooling (e.g. certipy auth or Rubeus
certificate-based TGT requests). `--machine` requests as SYSTEM,
`--install` loads it into the local store, `--output-pem` emits PEM
instead of PFX.

### Retrieve the domain's CA certificate and forge (DPERSIST1)

Post-domain-compromise persistence with a stolen CA key.

```
Certify.exe forge --ca-cert ca.pfx --upn administrator@corp.example.com
```

Outcome: signs a new certificate (arbitrary UPN/DNS SAN, SID extension
available) using the CA certificate — usable for authentication after the
CA is patched or offline; `--output-path` writes the forged PFX to disk
instead of the console.

### Manipulate a CA or template when you own rights

Enable attack conditions instead of waiting for them.

```
Certify.exe manage-ca --ca "dc01.corp.example.com\corp-DC01-CA" --esc6
Certify.exe manage-template --template CorpUser --supply-subject
```

Outcome: the first toggles the ESC6 (EDITF_ATTRIBUTESUBJECTALTNAME2) flag
on the CA; the second flips the template's "Enrollee Supplies Subject"
setting — both require the corresponding ManageCA/ownership rights, and
each supports undo (disable) by re-running.

### Capture output for long reports

Keep results as files on engagements.

```
Certify.exe enum-templates --out-file enum.txt --quiet
```

Outcome: redirects all output streams to `enum.txt` and suppresses the
logo — `--out-file` and `--quiet` are global flags valid on every command.
