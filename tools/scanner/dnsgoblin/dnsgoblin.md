---
name: "dnsgoblin"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "https://nullsecurity.net/tools/scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: other
license: custom
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# dnsgoblin

> Nasty creature that hunts for DNS servers on the wire by injecting crafted queries and watching who answers.

## What it's for

dnsgoblin (by atzeton of the Null Security team, 2011) is a one-argument
C tool that actively searches a network for live DNS servers: it injects a
hand-crafted raw-socket DNS query and then listens for replies, printing
every DNS server that responds to the probe. It builds its own packets
(IP/UDP headers included), so it needs root and takes the local interface
IP as its only argument — it is a discovery aid for finding resolvers to
test (open recursion, cache poisoning), not a scanner of domains. It runs
until interrupted.

## Typical use cases

### Hunt for DNS servers

Discover which hosts on the wire answer DNS.

```
sudo dnsgoblin 198.51.100.10
```

Outcome: injects the crafted DNS query from local IP 198.51.100.10 and
prints each answering server as it is observed; run until enough replies
have collected, then Ctrl-C to stop and clean up.

### Build a DNS server inventory file

Collect the finds into a list for follow-up testing.

```
sudo dnsgoblin 198.51.100.10 > dnslist.txt
```

Outcome: the same probe run with stdout redirected — every discovered DNS
server is appended to `dnslist.txt` (the upstream-recommended usage),
giving a resolver inventory to feed tools that test for open recursion or
spoofing behaviour.

*No corpus evidence — derived from upstream source (dnsgoblin.c in the
nullsecuritynet/tools repository, the packaged upstream).*
