---
name: "deblaze"
categories:
  - blackarch-scanner
version: "3.a24555d"
homepage: "https://github.com/SpiderLabs/deblaze"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "SpiderLabs/deblaze"
github-stars: 37
github-pushed: "2020-08-07"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# deblaze

> Enumerates and interrogates methods on Adobe AMF/BlazeDS (Flash Remoting) endpoints, including brute-forcing service and method names.

*No corpus evidence — derived from upstream documentation (SpiderLabs/deblaze README.txt — Jon Rose, Trustwave/SpiderLabs; archived, no longer maintained).*

## What it's for

deblaze (Jon Rose, SpiderLabs/Trustwave) attacks the server side of Flash Remoting: AMF endpoints such as BlazeDS expose named services with callable methods, and deblaze lets you enumerate or brute-force those service and method names (a wordlist `names.txt` ships alongside) and then call any discovered method with parameters of your choosing, printing the raw AMF response — verbose server errors frequently leak class structure, SQL fragments or stack traces worth mining. It also fingerprints the Flex/AMF technology in use. Two properties make these endpoints friendly to brute force: method names on most servers are case-insensitive, and the calls are HTTP POSTs that many servers do not log like ordinary requests. Python 2-era tooling (pyamf bundled); archived and unmaintained, so expect to read the responses rather than trust automation.

## Typical use cases

### Brute-force service names on an AMF endpoint

```
deblaze -u http://target.example.com/messagebroker/amf --bruteService names.txt -v
```

Outcome: each wordlist entry is tried as a service name against the endpoint and existing ones are reported (`-v` streams status messages); the discovered service names are the inputs for method enumeration. `-u` takes the AMF endpoint URL.

### Brute-force methods on a discovered service

```
deblaze -u http://target.example.com/messagebroker/amf -s UserService --bruteMethod names.txt
```

Outcome: for the named service, every wordlist entry is tried as a method name and the accepted ones are listed — the callable method set ready for interrogation. `--bruteService` and `--bruteMethod` are the mutually exclusive alternatives to directly naming a service or method.

### Call a method with parameters and credentials

```
deblaze -u http://target.example.com/messagebroker/amf -s UserService -m getUser -p '1|user' -c user:password
```

Outcome: invokes the method with the pipe-separated parameters, optionally under HTTP Basic credentials (`-c u:p`), and prints the endpoint's response verbatim — the response or its error text is the payoff, since AMF errors often expose object layouts and internal detail.
