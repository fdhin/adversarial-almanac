---
name: "honeycreds"
categories:
  - blackarch-defensive
version: "26.eaeb401"
homepage: "https://github.com/Ben0xA/HoneyCreds"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Ben0xA/HoneyCreds"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# honeycreds

> Injects decoy SMB/HTTP credentials into network traffic so poisoners like Responder capture them — and reveal themselves the moment they replay them.

## What it's for

HoneyCreds runs on an ordinary workstation and periodically performs SMB and HTTP authentication attempts using a decoy account against a hostname that does not exist. The credentials are worthless — nothing accepts them — but an attacker running a network poisoner (Responder and similar tools) that captures the segment's traffic will harvest them, and the moment those honey credentials are submitted anywhere else you have a high-confidence intrusion signal. Every injected attempt is logged locally, and the project ships with Splunk-based detection examples for turning replays into alerts. Point it at a plausible-looking but nonexistent host, use a realistic username and password, and never reuse real credentials.

## Typical use cases

### Configure a believable decoy account

Define the honey identity the injector will broadcast.

```
# honeycreds.conf
def_username = 'svc-backup'
def_domain   = 'CORP'
def_password = 'Password1!'
def_fqdn     = 'corp.example.com'
def_hostname = 'FILESRC02'
def_logfile  = 'honeycreds.log'
```

Outcome: the injector authenticates as this identity from now on; the hostname must not exist in your environment, and the username and domain should look plausible so the captured traffic blends in with real activity.

### Start injecting honey credentials

Begin broadcasting the decoy authentication attempts.

```
python3 honeycreds.py
```

Outcome: performs SMB and HTTP authentication attempts in a loop (paced by SMB_SLEEP and HTTP_SLEEP) and appends each attempt to the log file; any poisoner on the segment captures the decoy credentials as a side effect of listening.

### Detect replays of the honey account

Watch for the decoy credentials being used anywhere.

```
grep -ri svc-backup /var/log/
```

Outcome: any hit means someone submitted credentials that only ever existed in the injection traffic — a poisoner harvested and replayed them; treat the source as confirmed attacker activity and raise an alert (the project's Splunk examples formalize this).

*No corpus evidence — derived from upstream documentation.*
