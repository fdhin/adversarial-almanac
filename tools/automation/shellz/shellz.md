---
name: "shellz"
categories:
  - blackarch-automation
version: "161.0ed068f"
homepage: "https://github.com/4ndr34z/shells"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "4ndr34z/shells"
github-stars: 487
github-pushed: "2024-09-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shellz

> Interactive menu script ("shells" by 4ndr34z) for generating reverse shells — deep PowerShell support with AMSI bypasses, plus Python, netcat, PHP, Go, C/C++ and more — and it manages the listener, ngrok tunnel and Updog file server alongside.

## What it's for

Generating a reverse shell that is correctly formatted and reasonably evasive without hand-assembling it from a cheatsheet. Its strength is PowerShell: TCP/UDP/SSL shells, partial and full AMSI bypasses, ETW patching, Windows PowerShell vs PowerShell Core selection, reflective loading of a C# binary (Sharpcat), init options (cmd/conhost/powershell depending on payload length), and tricks like flooding the PowerShell operational log to push your script-block-logging event out of the window. Beyond PowerShell it covers Python (including embedded-Python payloads for hosts without Python installed and a VBA-macro variant), netcat (with looping callbacks), PHP/ASPX/JSP webshells, Go, C/C++ binaries and more. It also runs the supporting cast for you: it can start/stop the matching listener (including Metasploit multi/handler and SSL-aware listeners), an ngrok tunnel (generating payloads with the tunnel address), and Updog for file uploads/downloads. It is menu-driven — there is no flag-based CLI — and it generates shells rather than exploiting anything. Distinct from the similarly-named bi-zone/ShellZ project (a Go payload generator); this vault note tracks the 4ndr34z/shells script packaged by BlackArch and nixpkgs.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate a PowerShell reverse shell with its listener

```
shellz
```

Outcome: launches the interactive menu. Choose the PowerShell section, pick TCP/UDP/SSL, set the callback IP/port (defaults shown in the menu, default port 443), and select the AMSI-bypass level; the script prints the finished payload, starts the matching listener (in a new window on Linux, optionally Metasploit multi/handler), and offers clipboard copy. Pick the netcat or Python sections for non-Windows targets the same way.

### Expose the callback over ngrok without a routed host

From inside the running menu, start ngrok, then generate the payload:

```
shellz
```

Outcome: with ngrok running (started from the script's own menu option, which shows its status in every menu), payload generation offers to embed the ngrok forwarding address instead of your local IP — the target calls back through the tunnel to your machine behind NAT, and the same menu can stop the tunnel afterwards. The Updog option works the same way: start it from the menu, and the PowerShell payloads embed upload/download functions that talk to it.
