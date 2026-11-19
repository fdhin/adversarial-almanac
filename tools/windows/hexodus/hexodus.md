---
name: "hexodus"
categories:
  - blackarch-windows
version: "72.69a96f6"
homepage: "https://github.com/dsssssssm/hexodus"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-10"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "dsssssssm/hexodus"
github-stars: 6
github-pushed: "2025-07-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hexodus

> Profile-driven Active Directory enumeration over LDAP and SMB with a local database and web dashboard.

## What it's for

Hexodus is a Python framework for enumerating Active Directory and driving attacks over native Windows protocols — LDAP, SMB, RPC and friends, built on impacket and ldap3. You store connection settings once in a named profile, then invoke per-protocol modules (`ldap users`, `smb share`, ...) against a host; everything collected lands in a local SQLite database that a bundled Flask web interface visualizes. Reach for it when you want structured, repeatable AD enumeration that keeps state between queries instead of one-off impacket calls. WinRM and automated-attack modules were on the roadmap rather than shipped at the time of writing; it does not exploit anything by itself.

## Typical use cases

### Create a reusable connection profile

Store domain credentials and binding info once.

```
hexodus -c corp corp.example.com user 'Password1'
```

Outcome: saves a profile named `corp` holding the domain, username and password used by every later module invocation, so individual commands stay short.

### Enumerate domain users over LDAP

Pull user objects from a domain controller.

```
hexodus corp ldap 198.51.100.10 users
```

Outcome: binds to the LDAP service on the target with the profile credentials, enumerates user objects, and stores the results in the local SQLite database (and prints them).

### Inventory SMB shares

Map what a host exposes over SMB.

```
hexodus corp smb 198.51.100.10 share
```

Outcome: connects over SMB with the profile credentials and lists the target's shares, adding them to the collected dataset for later browsing.

### Discover available modules

See what each protocol can enumerate before running it.

```
hexodus corp ldap list
```

Outcome: prints the module names available for the `ldap` protocol (the same works for `smb`) — each name is what you append after the host argument to run that enumeration, e.g. `users` or `share`.

### Start the web dashboard

Browse everything collected so far.

```
hexodus -s
```

Outcome: launches the Flask web interface over the SQLite database; `hexodus -sw` stops it again.

*No corpus evidence — derived from upstream documentation.*
