---
name: "deathstar"
categories:
  - blackarch-automation
version: "60.d7bcbfd"
homepage: "https://github.com/byt3bl33d3r/DeathStar"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "byt3bl33d3r/DeathStar"
github-stars: 1631
github-pushed: "2024-01-23"
github-archived: true
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# deathstar

> Automated Domain Admin escalation that drives Empire's REST API once a first agent checks in.

## What it's for

DeathStar is a Python automation client for Empire: given a single Empire agent on one host, it chains the most commonly abused Active Directory misconfigurations — domain recon, privilege escalation, lateral movement, and active monitoring of compromised machines for new admin logins — until it holds Domain or Enterprise Admin rights, deliberately using only techniques unlikely to destabilize the network. It does nothing on its own: it requires a reachable Empire server with its REST API enabled plus matching credentials, and it starts a listener and reacts as agents appear. Note that upstream is archived (2024) and its API usage targets the Empire 3.x-era REST interface, so run it against an appropriately old Empire install rather than current releases.

## Typical use cases

### Point DeathStar at a local Empire server

Hand it the Empire REST credentials and let it take over once an agent lands.

```
deathstar -u empireadmin -p 'S3cur3P@ss'
```

Outcome: logs into the Empire REST API on the loopback interface (default 127.0.0.1:1337), automatically starts a listener, and waits; as soon as an Empire agent checks in it runs its recon → privesc → lateral-movement chain, printing each module execution and result.

### Target a remote Empire API

Run the automation from a different host than Empire.

```
deathstar -u empireadmin -p 'S3cur3P@ss' --api-host 198.51.100.10 --api-port 1337
```

Outcome: same automation against an Empire server at 198.51.100.10; `--api-port` overrides the default port 1337.

### Run with debug output

See exactly which Empire modules each phase executes.

```
deathstar -u empireadmin -p 'S3cur3P@ss' --debug
```

Outcome: verbose logging of every API call and parsed module result, including the JSON extracted from Empire's PowerShell module output — the fastest way to find which step of the chain stalls.

*No corpus evidence — derived from upstream documentation.*
