---
name: "pshitt"
categories:
  - blackarch-honeypot
version: "23.dae7931"
homepage: "https://github.com/regit/pshitt"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "regit/pshitt"
github-stars: 160
github-pushed: "2024-06-19"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pshitt

> Deliberately minimal fake SSH server whose only job is to write every brute-forced username/password pair to a JSON file.

## What it's for

pshitt ("Passwords of SSH Intruders Transferred to Text", by Eric Leblond) is a lightweight credential catcher: implemented on paramiko, it carries an SSH exchange just far enough to receive each authentication attempt, then appends one JSON record — username, password, source IP, source port, timestamp — to an output file. Nobody who authenticates gets a session or a shell, which is the point: park it where SSH brute-force noise arrives, then analyze the JSON for credential patterns such as passwords reused across your estate or targeted usernames. It listens on port 2200 by default; use `-p` (with root for privileged ports, or port-forwarding from 22) to place it in the noise path. It does not emulate any post-login behaviour and stores nothing but the credentials.

## Typical use cases

### Collect brute-force credentials

Run the catcher and let attempts accumulate.

```
pshitt -o passwords.json
```

Outcome: a fake SSH server listens on TCP/2200 and every authentication attempt appends one JSON object (`username`, `password`, `src_ip`, `src_port`, `timestamp`) to passwords.json.

### Run as a daemon on port 22

Place the catcher on the real SSH port and detach it.

```
sudo pshitt -p 22 -o passwords.json -D -l pshitt.log
```

Outcome: after binding TCP/22 the process daemonizes (`-D`), keeps its own diagnostics in pshitt.log, and continues appending captured credentials to passwords.json.

*No corpus evidence — derived from upstream documentation.*
