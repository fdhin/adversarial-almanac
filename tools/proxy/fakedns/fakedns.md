---
name: "fakedns"
categories:
  - blackarch-proxy
version: "118.39609da"
homepage: "https://github.com/Crypt0s/FakeDns"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Crypt0s/FakeDns"
github-stars: 586
github-pushed: "2021-08-27"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fakedns

> Regular-expression-driven DNS server: answer the queries you choose with records you control, forward everything else upstream unchanged.

## What it's for

FakeDns is a Python DNS server for interception work. Incoming queries are matched against Python regular expressions in a rule file; matches get your crafted answer, and non-matching names are resolved normally through the system's resolver and passed back to the client. Reach for it when steering a target's connections — point a domain's A record at your listener so its traffic lands on you, or hand out TXT records on demand — without breaking resolution for everything else the target does. It also implements round-robin answers and DNS rebinding (serve one IP for the first N queries, then switch), which defeats naive same-IP checks in applications. Supported record types: A, AAAA, TXT, PTR and SOA (MX and CNAME are listed as in-progress). It needs UDP port 53, so run it as root or with port 53 mapped in from a container.

## Typical use cases

### Serve a chosen record for a target domain

`dns.conf`:

```
A app.example.com 198.51.100.10
```

Then start the server:

```
./fakedns.py -c dns.conf
```

Outcome: the server listens on port 53; queries for `app.example.com` resolve to 198.51.100.10 while every name that matches no rule is forwarded to the system resolver and answered truthfully. Rules follow `[record type] [python regex] [answer]`, so wildcards like `A (www\.)?example\.com self` work too — the answer `self` expands to the machine's current local IP. Edit the rules and restart the server to change answers mid-run.

### Rotate answers with round-robin

`dns.conf`:

```
A pool.example.com 198.51.100.10,198.51.100.11,198.51.100.12
```

Then start the server:

```
./fakedns.py -c dns.conf
```

Outcome: each matching query is answered with the next IP in the comma-separated list, one address per response, iterating in order — spread a target's repeated connections across several hosts you control.

### Rebind a domain after N queries

`dns.conf`:

```
A app.example.com 198.51.100.10 5%198.51.100.99
```

Run with rebinding enabled:

```
./fakedns.py -c dns.conf --rebind
```

Outcome: the first 5 queries for `app.example.com` resolve to 198.51.100.10; every query after that resolves to 198.51.100.99 — the classic way to pass an initial validation against one address and then make the client talk to a second host. The rebind answer requires `--rebind`; a comma-separated list there is round-robined like a normal rule.

### Bind the listener to one interface

```
./fakedns.py -c dns.conf -i 198.51.100.10
```

Outcome: the DNS service answers on 198.51.100.10 specifically — useful on a multi-homed host where queries should be accepted on exactly one address.
