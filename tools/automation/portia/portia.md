---
name: "portia"
categories:
  - blackarch-automation
version: "39.2e6e608"
homepage: "https://github.com/SpiderLabs/portia"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "SpiderLabs/portia"
github-stars: 500
github-pushed: "2020-08-07"
github-archived: true
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# portia

> SpiderLabs' internal-network attack chain automator: hand it one low-privileged domain credential and it works the classic AD kill-chain — admin-rights checks, domain-admin discovery, SYSVOL password grabs, token impersonation, mimikatz on the DC — spreading host to host.

## What it's for

Doing the post-foothold legwork of an internal network engagement automatically. Given compromised credentials, Portia enumerates which hosts the account has admin rights on, checks domain-admin group membership, hunts passwords in SYSVOL, uses impersonation tokens, runs Mimikatz-style hash dumps on reachable machines (up to the domain controller), and recycles newly found hashes and passwords into the next hop — privilege escalation and lateral movement as a loop. It is msfvenom-style on the command line: positional targets plus flags for domain/credentials, with module selection (`-M`) and module options (`-o`), plus conveniences like AMSI/AppLocker bypass and PowerShell obfuscation toggles. Upstream explicitly states it is no longer maintained (archived), its heyday was the 2016–2018 AD attack era, and modern defenders/EDR handle those techniques very differently — so treat it as a legacy chain-automation experiment, and expect its heavy dependency list (a specific impacket checkout, ntdsxtract, volatility, and more) to need assembling on a Python 2 environment.

*No corpus evidence — derived from upstream documentation and source.*

## Typical use cases

### List the available modules

```
portia -L
```

Outcome: prints every payload/exploitation module Portia can drive with the `-M` flag (the module catalog is the tool's real feature list) so you can pick a specific technique by name instead of running the whole chain.

### Run the automated chain with a foothold credential

```
portia 198.51.100.0/24 -d corp.example.com -u user -p 'Password1'
```

Outcome: sweeps the target range with the given domain credential, and for each host where the account has rights it executes the escalation/lateral-movement sequence — checking domain-admin membership, pulling SYSVOL passwords, impersonating tokens where available, dumping hashes — then feeds every recovered credential back into the next round. Watch it walk the kill-chain steps live; anything it compromises prints to the console.

### Run only a specific module with options

```
portia 198.51.100.10 -d corp.example.com -u user -p 'Password1' -s -M rdp -o OPTION=value
```

Outcome: `-s` skips the lateral movement/privilege escalation machinery and runs only the selected module (`-M`, e.g. `rdp`, `wdigest`, `mssqlbrute`, `files` — `portia -L` prints the full catalog) against the given host, with module options passed through `-o` — the surgical mode for one technique on one box. Add `-amsi`, `-bypass` and `-obfs` to arm the PowerShell delivery helpers when AV/AppLocker are in play.
