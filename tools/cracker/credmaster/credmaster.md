---
name: "credmaster"
categories:
  - blackarch-cracker
version: "206.0a79f34"
homepage: "https://github.com/knavesec/CredMaster"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "knavesec/CredMaster"
github-stars: 1299
github-pushed: "2025-03-19"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# credmaster

> Plugin-based password-spraying framework that rotates the source IP for every attempt through dynamically created AWS FireProx endpoints.

## What it's for

CredMaster refactors the CredKing/FireProx lineage into a modular spraying tool: for each authentication attempt it spins up an AWS API Gateway passthrough endpoint in front of the target login, so every request egresses from a different IP address and per-source throttling never pins the spray to one origin. Authentication plugins cover OWA, EWS, ADFS, MSOL, MSGraph, Azure AD Seamless SSO, Azure Key Vault, Okta, Fortinet VPN, generic HTTP Basic/Digest/NTLM and custom HTTP POST forms; enumeration plugins for O365 and Gmail validate usernames without authenticating. AWS API access keys are required, and the tool offers password delay counters for lockout-policy evasion, notification systems (Keybase, Slack, Discord, Teams, Pushover, ntfy) and timed WeekdayWarrior spraying. Account lockout is the operator's responsibility — know the target's reset policy before spraying.

## Typical use cases

### Spray a password list across a user list

The core operation: rotate IPs on every request while spraying.

```
credmaster --plugin msol --access_key AKIAIOSFODNN7EXAMPLE --secret_access_key wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY -u users.txt -p passwords.txt
```

Outcome: creates FireProx endpoints in front of the Microsoft Online login, sprays one password per round across the user file with a different requesting IP each time, and prints each valid username/password pair as it is found.

### Drive the spray from a config file

Keep target, keys and timing options in one file instead of CLI flags.

```
credmaster --config config.json
```

Outcome: reads the plugin selection, AWS credentials, user/password sources and delay settings from config.json and runs the spray exactly as configured there. Upstream documents the equivalent `python3 credmaster.py` invocation for source checkouts.

*No corpus evidence — derived from upstream documentation.*
