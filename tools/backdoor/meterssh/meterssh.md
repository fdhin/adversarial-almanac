---
name: "meterssh"
categories:
  - blackarch-backdoor
version: "18.9a5ed19"
homepage: "https://github.com/trustedsec/meterssh"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "trustedsec/meterssh"
github-stars: 530
github-pushed: "2017-04-13"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# meterssh

> Meterpreter over SSH: shellcode injected on the victim opens a local bind shell and tunnels it home inside an ordinary-looking SSH session.

## What it's for

Making Metasploit's Meterpreter traffic indistinguishable from legitimate SSH. The implant (`meterssh.py`, a Python 2 script for Windows victims) injects an unmodified `windows/meterpreter/bind_tcp` shellcode into memory, then opens an SSH connection to the operator's server and forwards the shellcode's bind port (localhost:8021) back over that tunnel — all command-and-control flows through SSH, never directly over the network. On the operator side, `monitor.py` watches for the forwarded port to appear and launches the matching Metasploit handler automatically, so the session is just Meterpreter against localhost. Use it to test whether egress rules that permit SSH also let Meterpreter through, or to demonstrate SSH-tunneled C2 to a blue team. Rerouting only requires editing four fields in the implant — no shellcode changes. Limitations: it needs valid SSH credentials embedded in the implant, Python on the victim, and it is unmaintained 2016-era code.

## Typical use cases

### Point the implant at your SSH server

Set the connection fields at the top of `meterssh.py`:

```
user = "sshuser"
password = "sshpw"
rhost = "198.51.100.10"
port = "22"
```

Outcome: when the edited `meterssh.py` runs on a Windows host, it injects the bind_tcp Meterpreter shellcode into memory, connects to the SSH server at 198.51.100.10 and forwards the victim's localhost:8021 to the operator's localhost:8021. These four fields are the only configuration — the shellcode never needs regenerating.

### Catch the session with the monitor

On the operator machine (the SSH server):

```
python2 monitor.py
```

Outcome: `monitor.py` polls localhost:8021; as soon as the victim's SSH tunnel brings the port up, it spawns Metasploit with the matching handler and you receive an interactive Meterpreter session whose traffic is entirely inside the SSH connection. On BlackArch the packaged wrapper for this step is `meterssh-monitor`.

*No corpus evidence — derived from upstream documentation.*
