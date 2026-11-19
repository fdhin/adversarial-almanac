---
name: "net-creds"
categories:
  - blackarch-sniffer
version: "87.07a25e1"
homepage: "https://github.com/DanMcInerney/net-creds"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/net-creds"
github-stars: 1869
github-pushed: "2023-11-02"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# net-creds

> Strips credentials and other secrets out of live traffic or a pcap — logins, hashes, URLs, POSTs — in one pass.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Snarfing plaintext credentials wholesale from an interface or a capture file. One run picks up URLs visited, POST bodies, HTTP form and basic-auth logins, FTP/IRC/POP/IMAP/Telnet/SMTP logins, SNMP community strings, NTLMv1/v2 hashes across their protocols (HTTP, SMB, LDAP, ...) and Kerberos tokens. It reassembles fragmented packets and identifies services by payload rather than port number, so credentials riding on unexpected ports are still caught. By Dan McInerney; his earlier and narrower [[creds]] harvests a fixed FTP/POP/IMAP/HTTP/IRC protocol set — net-creds is the broader tool. Use on networks you are authorized to assess; it needs root for live capture but not for pcap parsing.

## Typical use cases

### Sniff the auto-detected interface

```
sudo net-creds
```

Outcome: the interface is chosen automatically and every credential-shaped artifact — logins, hashes, URLs, POST payloads, community strings — is printed as it is reassembled from live traffic.

### Target one interface and exclude your own host

```
sudo net-creds -i eth0 -f 198.51.100.10
```

Outcome: capture runs on eth0 with all packets to and from 198.51.100.10 ignored — useful to keep your own scanning box's traffic out of the results.

### Mine an existing pcap

```
net-creds -p capture.pcap
```

Outcome: the capture file is parsed offline with the same output as live mode; no privileges required, making it a quick triage step for pcaps collected elsewhere.
