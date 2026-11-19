---
name: "ssh-honeypot"
categories:
  - blackarch-honeypot
version: "113.4bda71c"
homepage: "https://github.com/droberson/ssh-honeypot"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-01"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "droberson/ssh-honeypot"
github-stars: 674
github-pushed: "2024-10-29"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ssh-honeypot

> Minimal libssh-based fake sshd that records the IP, username and password of every connection attempt — and nothing else.

## What it's for

ssh-honeypot (droberson) is a low-interaction SSH decoy: it completes the server side of the SSH handshake so clients will try to authenticate, then logs the source IP, username and password of each attempt. There is no login and no shell — an attacker who "succeeds" gets nothing — which suits its stated purpose of gathering rudimentary intelligence on brute-force campaigns (the author pairs it with his sshunt shunt for CTF use). Around that core it adds client fingerprinting via HASSH, banner spoofing to blend into a real estate, JSON logging to a file or a remote UDP collector for Splunk/Elastic ingestion, and privilege dropping after binding a privileged port. An RSA host key must be supplied with `-r` before it will serve; it is a log-first sensor, not a hardened production service.

## Typical use cases

### Capture credential attempts

Start the decoy with a generated host key.

```
ssh-honeypot -r ./ssh-honeypot.rsa
```

Outcome: the fake sshd listens on its default port and writes one log record per connection attempt with source IP, username and password; the key file passed with `-r` is the only required argument.

### Bind port 22 without staying root

Serve on the real SSH port but shed privileges after binding.

```
sudo ssh-honeypot -p 22 -u nobody -r ./ssh-honeypot.rsa
```

Outcome: the listener binds TCP/22 as root, drops to nobody for the session loop (chowning the log file to that user), so compromising the decoy does not yield a root shell.

### Stream JSON logs to a collector

Centralize captures from several sensors.

```
ssh-honeypot -j creds.json -J 198.51.100.10 -P 5140 -r ./ssh-honeypot.rsa
```

Outcome: each attempt is appended locally to creds.json in JSON and simultaneously shipped as JSON over UDP to 198.51.100.10:5140, ready for Splunk or Elastic listeners fronting multiple ssh-honeypot instances.

*No corpus evidence — derived from upstream documentation.*
