---
name: "kippo"
categories:
  - blackarch-honeypot
version: "287.b9eb06a"
homepage: "https://github.com/desaster/kippo"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "desaster/kippo"
github-stars: 1715
github-pushed: "2023-11-19"
github-archived: false
github-issues: 83
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kippo

> Medium-interaction SSH honeypot that drops attackers into a fake root shell on a simulated Debian filesystem and records the entire session for replay.

## What it's for

Kippo (desaster) is the classic medium-interaction SSH honeypot and the ancestor of most later SSH honeypots. It accepts logins with passwords from a configurable set, then hands the "attacker" a fake root shell on a simulated Debian 5.0 filesystem (with addable fake file contents, so `cat /etc/passwd` returns something plausible) and records the whole interaction — commands, output, and even original keystroke timing — so sessions can be replayed from the logs. Files the attacker downloads with wget are quarantined under `dl/` for inspection, and built-in trickery (an `exit` that doesn't exit, connections that appear to hang) keeps naive intruders busy. By default it listens on port 2222 because binding 22 requires root; the documented deployment is port-forwarding from 22. Development has moved to the cowrie fork — Kippo itself is a Python 2/Twisted application configured through `kippo.cfg`.

## Typical use cases

### Start the honeypot

Launch kippo with its `kippo.cfg` settings.

```
./start.sh
```

Outcome: start.sh runs kippo in the background via twistd; the fake sshd listens on the configured port (default 2222), session transcripts accumulate under `log/tty/`, attacker downloads land in `dl/`, and debug output goes to `log/kippo.log`.

### Run attached to the terminal

Keep kippo in the foreground to watch sessions live while tuning it.

```
twistd -y kippo.tac -n
```

Outcome: the Twisted application runs without daemonizing and logs connections and activity to stdout — the documented way to observe which credentials `kippo.cfg` accepts before leaving the decoy unattended.

*No corpus evidence — derived from upstream documentation.*
