---
name: "honssh"
categories:
  - blackarch-honeypot
version: "1.1.2.r2.g821ce87"
homepage: "https://github.com/tnich/honssh"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-30"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "tnich/honssh"
github-stars: 374
github-pushed: "2022-01-02"
github-archived: true
github-issues: 9
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# honssh

> High-interaction SSH honeypot proxy that splits each connection in two — attacker to HonSSH, HonSSH to a real honeypot — so full sessions are captured while the attacker sees a genuine system.

## What it's for

HonSSH (Thomas Nicholson, inspired by and partly built on Kippo) is designed to run in front of a high-interaction honeypot: it terminates the attacker's SSH connection on one side and opens a second, real SSH connection to the honeypot machine on the other, relaying traffic while logging the entire conversation. Because the backing system is real, attackers interact with a genuine OS rather than a scripted shell, and everything they do is still recorded. The behaviour — listen ports, the upstream honeypot's address, which credentials may pass — is set in `honssh.cfg` (from `honssh.cfg.default`) with acceptable accounts in `users.cfg`. The project is no longer maintained, so pair it with a disposable, isolated honeypot image and expect Python-2-era dependencies.

## Typical use cases

### Start and manage the proxy

Bring the honeypot front-end up with its bundled control script.

```
./honsshctrl.sh start
```

Outcome: honsshctrl.sh launches the HonSSH Twisted application (`honssh.tac`) in the background; the same script also accepts `stop`, `restart`, `clean` (stale pid file) and `tail {app|daily|dl}` to follow its logs once sessions start flowing.

### Run in the foreground while configuring

Attach the proxy to the terminal to watch handshakes during setup.

```
twistd -y honssh.tac -n
```

Outcome: the Twisted reactor runs honssh.tac without daemonizing, logging to stdout — useful for verifying listen ports and the upstream honeypot settings in honssh.cfg with a test SSH client before exposing the decoy.

*No corpus evidence — derived from upstream documentation.*
