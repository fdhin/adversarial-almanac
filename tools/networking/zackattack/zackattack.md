---
name: "zackattack"
categories:
  - blackarch-networking
version: "5.1f96c14"
homepage: "https://github.com/urbanesec/ZackAttack/"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "urbanesec/ZackAttack"
github-stars: 261
github-pushed: "2016-08-06"
github-archived: false
github-issues: 55
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zackattack

> NTLM relay toolset whose rogue HTTP/SMB servers pool captured authentications and replay them per rule, with a web console.

## What it's for

ZackAttack! is a Ruby toolset for NTLM authentication relaying with a different design from classic relay tools: rogue HTTP and SMB servers capture NTLM/NTLMv2 authentications, keep the sockets alive, and recycle the credentials against pooled targets — Exchange Web Services, LDAP (relevant for domain controllers), and SMB — instead of closing after one replay. Rule files define actions to fire automatically when a user in a given group authenticates; payload helpers coax browsers into auto-authenticating (including via UNC/SMB paths); and a SOCKS proxy lets existing tools relay through the captured sessions. It is 2016-era alpha code written for Ruby 1.9 and no longer maintained — expect rough edges and verify behavior in a lab before relying on it.

## Typical use cases

### Start the rogue servers and the management web UI

Bring up the relay infrastructure.

```
ruby zackattack.rb
```

Outcome: Starts the rogue HTTP and SMB servers plus a management web interface on http://localhost:4531/ — captured authentications, active relay sessions, rules and payloads are all driven from that page. Components: the rogue servers funnel authentications, the clients replay them at target services, the rules auto-fire on matching users/groups, and the payloads generate auto-authentication attempts.

*No corpus evidence — derived from upstream documentation.*
