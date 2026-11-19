---
name: "sipbrute"
categories:
  - blackarch-voip
version: "11.5be2fdd"
homepage: "https://github.com/packetassailant/sipbrute"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "packetassailant/sipbrute"
github-stars: 14
github-pushed: "2016-03-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sipbrute

> Offline dictionary cracker for SIP digest authentication — recovers the password behind a captured REGISTER Authorization response.

## What it's for

Turning a sniffed SIP exchange into a password. sipbrute (Go, by packetassailant) takes a text file containing a SIP REGISTER request with its `Authorization: Digest ...` header — the username, realm, URI, nonce and response captured from the wire — and a wordlist, then recomputes the RFC 3261 MD5 chain (H1 = username:realm:password, H2 = method:uri, response = MD5(H1:nonce:H2)) for every candidate at high concurrency until one matches the observed response hash. It is purely offline: it never contacts the registrar, so it leaves no failed-authentication traces, but it cannot run without a captured digest exchange. For online password testing against SIP servers see [[sippts]] (`rcrack`), and for extracting digests from PCAP files see `sippts dump`.

## Typical use cases

### Crack a captured REGISTER digest

```
sipbrute -path register.txt -dict wordlist.txt
```

Outcome: prints `Starting crack of hash: <response-hash>` and, on success, `Password match: <word> on hash <hash>`. `register.txt` holds the captured request including the full Authorization header — the `samples/register.txt` file in the source tree shows the expected format; large wordlists run in seconds on a modern CPU.

### Log every derivation attempt

```
sipbrute -path register.txt -dict wordlist.txt -verbose
```

Outcome: prints each candidate derivation to stdout — slower, but it shows the hash material being processed and is useful when the capture's formatting (or the hash itself) needs debugging.

*No corpus evidence — derived from upstream documentation.*
