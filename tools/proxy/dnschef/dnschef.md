---
name: "dnschef"
categories:
  - blackarch-proxy
version: "17.a395411"
homepage: "http://thesprawl.org/projects/dnschef/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "iphelix/dnschef"
github-stars: 1078
github-pushed: "2024-08-16"
github-archived: false
github-issues: 31
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnschef

> DNS proxy for penetration testers: forge answers for the domains you pick, transparently proxy the rest to real resolvers.

## What it's for

DNSChef is a Python DNS proxy that spoofs responses for selected domains while forwarding all other queries to a real upstream resolver, so the target keeps working everywhere except where you want it redirected. It is the tool of choice when the application under test ignores system proxy settings (common with mobile apps): move its DNS to the DNSChef host and its connections follow the fake records to your interception server. It supports fake A/AAAA/MX/CNAME/NS records, inclusive (`--fakedomains`) and exclusive (`--truedomains`) domain lists with wildcards, per-domain records from a file, custom upstream nameservers, IPv6, TCP mode, and a full transparent-proxy monitoring mode. It only controls name resolution — pair it with a web proxy or listener on the fake address to actually capture the traffic.

## Typical use cases

### Monitor which domains a client resolves

```
dnschef.py
```

Outcome: runs in full proxy mode — all queries are forwarded to the default upstream (8.8.8.8) and answered truthfully, while each lookup is logged, e.g. `[12:00:00] 127.0.0.1: proxying the response of type 'A' for example.com`. Point the client's nameserver at this host first; this mode gives a quick map of the domains an application contacts.

### Redirect chosen domains to your interception host

```
dnschef.py --fakeip 198.51.100.10 --fakedomains example.com,cdn.example.com
```

Outcome: A queries for the listed domains resolve to 198.51.100.10 (your listener or web proxy), while every other domain resolves truthfully upstream. Without `--fakedomains`, all A queries are spoofed; fake IPv6, MX, CNAME and NS answers are set with `--fakeipv6`, `--fakemail`, `--fakealias` and `--fakens`.

### Spoof everything except chosen domains

```
dnschef.py --fakeip 198.51.100.10 --truedomains update.example.com,ntp.example.com
```

Outcome: inverted filter — every domain is faked to 198.51.100.10 except the listed ones, which keep their true records. Use it to keep a target's update or licensing checks healthy while capturing the rest of its traffic.

### Define per-domain records in a file

`dnschef.ini`:

```
example.com=198.51.100.10
v6.example.com=2001:db8::10
```

Then run:

```
dnschef.py --file dnschef.ini -q
```

Outcome: each listed domain resolves to its own address (IPv6 values are auto-detected), with the banner suppressed by `-q`. File entries take precedence over command-line fake values.

### Choose the upstream resolver and listen port

```
dnschef.py --nameservers 198.51.100.53#53 -p 5353 -t
```

Outcome: non-matching queries are resolved via 198.51.100.53 instead of the default, the listener answers on port 5353 (port 53 needs no root this way), and `-t` proxies over TCP instead of UDP. `--nameservers` accepts a comma-separated list with `IP#port` per entry, and a random member is used per proxied query.

*No corpus evidence — derived from upstream documentation.*
