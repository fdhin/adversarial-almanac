---
name: "stews"
categories:
  - blackarch-webapp
version: "1.0.0.r7.gc7bba5a"
homepage: "https://github.com/PalindromeLabs/STEWS"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "PalindromeLabs/STEWS"
github-stars: 382
github-pushed: "2022-01-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# stews

> Security Tool for Enumerating WebSockets: discovery, fingerprinting, and CVE checks.

## What it's for

STEWS (PalindromeLabs, presented at OWASP AppSec US 2021) is a suite of three tools that
address the three gaps in WebSocket security testing: discovering WebSocket endpoints at
scale, fingerprinting the WebSocket server implementation, and testing known WebSocket
vulnerabilities (CSWSH and specific CVEs). It exists because mainstream scanners and Burp
historically lacked scriptable WebSocket support. The packaged suite installs three
commands: `stews-discovery`, `stews-fingerprint`, and `stews-vuln-detect`. Discovery drives
a custom ZGrab2 fork (must be installed and on PATH, along with `jq`); the other two are
self-contained Python 3 tools.

## Typical use cases

### Discover WebSocket endpoints across many domains

Feed a URL list through WebSocket handshakes to find live endpoints.

```
stews-discovery
```

Outcome: reads `./known-endpoints.txt` (one URL per line — supply your own domain/wordlist file; the upstream default mixes domains with common WebSocket paths such as `/ws`, `/socket.io/?EIO=3&transport=websocket`), sends WebSocket upgrade requests via zgrab2, and appends every server answering `101 Switching Protocols` to `STEWS-discovery-output.txt`.

### Fingerprint a WebSocket server

Identify the implementation behind a known endpoint.

```
stews-fingerprint -u wss://target.example.com -a
```

Outcome: runs the full test series (opcodes, RSV bits, versions, extensions, subprotocols, long payloads, hybi) against the endpoint and reports which known implementation the behavioral fingerprint matches. Add `-n` for `ws://`, `-k` to ignore invalid certificates, and `-g` to generate a fingerprint file for a server you control.

### Test a WebSocket endpoint for known CVEs

Check an endpoint against the built-in vulnerability test cases.

```
stews-vuln-detect -1 -n -u target.example.com:8080
```

Outcome: `-1` performs the generic Cross-Site WebSocket Hijacking test (combine with `-o https://evil.example` to test origin-based protections); `-2`/`-3`/`-4` test CVE-2021-32640 (ws), CVE-2020-7662/7663 (faye), and CVE-2020-27813 (Gorilla) respectively. Results print per test case.

No corpus evidence — derived from upstream documentation.
