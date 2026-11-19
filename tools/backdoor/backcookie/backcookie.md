---
name: "backcookie"
categories:
  - blackarch-backdoor
version: "51.6dabc38"
homepage: "https://github.com/mrjopino/backcookie"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mrjopino/backcookie"
github-stars: 64
github-pushed: "2016-10-18"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# backcookie

> Minimal cookie-based web backdoor — a six-line PHP stub executes base64 commands delivered in an HTTP cookie, driven by a small Python client that keeps the traffic inside ordinary-looking cookie headers.

## What it's for

Keeping command execution on a compromised web server looking like innocuous browsing traffic. The server side is a tiny PHP stub (the whole implant) that runs `passthru(base64_decode($_COOKIE[<key>]))`: every request's cookie value is a base64-encoded shell command, so no POST body or suspicious URL parameters ever appear; the client (`backcookie.py`) wraps that protocol, sending each entered command as the cookie and printing the response. Reach for it when a foothold on a PHP host must survive casual log review; it is not stealthy against anyone who inspects cookie contents, offers no encryption or persistence beyond the file itself, and is a 2016-era micro-tool — one channel, one command per request. The cookie name is the shared secret: client and stub must agree on it.

## Typical use cases

### Operate the backdoor from the client

Connect the Python client to a planted stub and run commands.

```
python backcookie.py -u http://target.example.com/shell.php -c yourcookie
```

Outcome: an interactive prompt opens (`@pwned:~$`) where each entered command is base64-encoded, sent as the `yourcookie` cookie to the stub, executed server-side via `passthru`, and its output printed. The long forms `--url` and `--cookie` are equivalent.

### Enumerate the target and quit cleanly

Use the built-in session commands.

```
binfo
```

Outcome: `binfo` makes the stub report objective information about the compromised host (server-side details), and `exit` closes the session without leaving trace output in the client terminal. Any other input is passed through as a raw shell command.

*No corpus evidence — derived from upstream documentation.*
