---
name: "ridrelay"
categories:
  - blackarch-recon
version: "34.f2fa99c"
homepage: "https://github.com/skorov/ridrelay"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "skorov/ridrelay"
github-stars: 400
github-pushed: "2020-05-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ridrelay

> Enumerate domain usernames and password policy with zero credentials by relaying coerced SMB/HTTP authentication and RID-cycling over lsarpc.

## What it's for

RidRelay gets you a domain user list when you hold no credentials on an internal network. It starts local SMB and HTTP servers, waits for an incoming authentication (in practice poisoned/forced by Responder), relays those credentials to a target host you choose, and then — riding the relayed low-privilege SMB session — cycles up to 50,000 RIDs through the lsarpc pipe to enumerate domain usernames, and pulls the password policy via samr. The relay target must be a domain member with SMB signing disabled (find one with a signing check). It needs Python 3.6+ and impacket. It is strictly an enumerator: no credential capture (relay only), and the username list it produces is the input for spraying/kerberoasting tooling, not the end of the chain.

## Typical use cases

### Relay authentication into username enumeration

```
python ridrelay.py -t 198.51.100.10
```

Outcome: stands up the SMB/HTTP relay servers and waits; once a victim authenticates (start Responder alongside to poison requests toward this host), the credentials relay to `198.51.100.10`, and the tool streams enumerated domain usernames to the terminal, followed by the domain password policy.

### Save the user list for follow-on tooling

```
python ridrelay.py -t 198.51.100.10 -o domain-users.txt
```

Outcome: same relay-and-enumerate flow, but every discovered username is also written to `domain-users.txt` — a ready-made input file for password spraying or username-enumeration cross-checks.

*No corpus evidence — derived from upstream documentation.*
