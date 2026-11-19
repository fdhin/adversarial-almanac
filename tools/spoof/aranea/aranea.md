---
name: "aranea"
categories:
  - blackarch-spoof
version: "6.469b9ee"
homepage: "https://github.com/TigerSecurity"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "TigerSecurity/aranea"
github-stars: 17
github-pushed: "2011-09-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aranea

> Multithreaded libpcap-based DNS spoofer (Emanuele Acri, Tiger Security) that answers live queries from an /etc/hosts-style rules file where hostnames are regular expressions.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Run it on a machine that sees a network's DNS traffic and it watches for queries whose name matches a rule in the supplied hosts file, then injects a forged reply carrying the IP of your choice. The distinguishing feature is regex matching: a single line can cover one host, a domain and all its subdomains, or every name a client asks for. It is a pure spoofing daemon — it does not cache, proxy or store anything, and it does not attack the real resolver; the forged reply simply races it. Needs root and libpcap. Note the tagline is the upstream README's own: version 1.0, 2011, and unchanged since — treat it as a lab/teaching tool rather than a maintained one.

## Typical use cases

### Redirect a domain and its subdomains

One sentence of intent: point everything under one domain at a host you control.

```
echo '198.51.100.10 example\.com' > hosts.txt
sudo aranea -f hosts.txt
```

Outcome: any client whose port-53 traffic passes the interface aranea listens on receives `198.51.100.10` as the answer for `example.com` and any subdomain (the regex `example\.com` matches at any depth). Escape the dots so they match literally.

### Answer every DNS query with one address

One sentence of intent: build a crude captive-portal effect where every lookup lands on the same box.

```
echo '198.51.100.10 .' > hosts.txt
sudo aranea -f hosts.txt
```

Outcome: the bare `.` regex matches every queried name, so all resolutions resolve to `198.51.100.10`; useful in a lab to force clients onto a local server regardless of what they ask for.

### Sniff and spoof on a specific interface with a custom filter

One sentence of intent: scope the attack to one segment or VLAN and watch it work.

```
sudo aranea -f hosts.txt -i eth1 -F 'udp port 53 and src net 198.51.100.0/24' -v
```

Outcome: `-i` selects the interface, `-F` applies an arbitrary pcap filter (here limiting spoofing to queries from one subnet), and `-v` raises verbosity so matched queries and injected replies are printed as they happen.
