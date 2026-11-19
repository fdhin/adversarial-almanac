---
name: "mitm6"
categories:
  - blackarch-scanner
version: "33.8e75884"
homepage: "https://github.com/fox-it/mitm6"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "fox-it/mitm6"
github-stars: 1930
github-pushed: "2024-02-20"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mitm6

> Become the default DNS server of an IPv4 network by answering DHCPv6 over IPv6 — the first half of a DNS-takeover plus credential-relay attack.

## What it's for

mitm6 abuses Windows' default IPv6 behavior: it answers DHCPv6 requests, hands victims a link-local IPv6 address and installs the attacker's machine as their DNS server. It then selectively spoofs DNS answers for domains of your choosing, redirecting victim traffic to the attacker host. It is built to run next to impacket's ntlmrelayx (WPAD spoofing and NTLM relay) or krbrelayx (Kerberos-over-DNS relay) — mitm6 itself only hijacks name resolution, it is not a full traffic relay. It needs root (raw sockets via Scapy), and it deliberately keeps DHCPv6 leases and DNS TTLs short so the network recovers within minutes of the tool stopping.

## Typical use cases

### Poison DNS for a domain

Answer DHCPv6 and take over name resolution for hosts in the target domain.

```
mitm6 -d example.corp -i eth0
```

Outcome: mitm6 advertises itself on eth0 via DHCPv6; Windows clients switch their DNS server to the attacker host as their leases renew, and each hijacked victim is listed. DNS queries matching example.corp (substring match, so subdomains included) are answered with the attacker's address.

### Scope the spoof with allowlist and blocklist

Keep specific hosts or domains out of the attack while spoofing several domains.

```
mitm6 -d corp.example.com -d it.example.com -b secure.corp.example.com
```

Outcome: only the allowlisted domains are spoofed, and the blocklisted name always keeps its legitimate resolution (the blocklist wins over the allowlist), keeping sensitive hosts out of the path.

### Run without rogue Router Advertisements

Stay quieter on networks that detect or block rogue RAs.

```
mitm6 -d example.corp --no-ra
```

Outcome: mitm6 skips Router Advertisement broadcasts and relies purely on DHCPv6, which is sufficient for the DNS takeover against default Windows hosts.

### Pair with ntlmrelayx for WPAD credential relay

Run the classic mitm6 plus ntlmrelayx combination for relay attacks.

```
mitm6 -d example.corp -i eth0
ntlmrelayx.py -6 -wh wpad -t smb://198.51.100.10
```

Outcome: victims resolving the wpad hostname reach the relay instead of a real proxy, and the authentication they send is relayed to the target host; captured credentials and SMB interactions appear in the ntlmrelayx output. For Kerberos-over-DNS relaying, run krbrelayx alongside and pass `--relay web01.example.corp` to mitm6 instead.
