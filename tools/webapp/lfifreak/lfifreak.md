---
name: "lfifreak"
categories:
  - blackarch-webapp
version: "22.86b97dd"
homepage: "https://github.com/OsandaMalith/LFiFreak/"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "OsandaMalith/LFiFreak"
github-stars: 321
github-pushed: "2026-07-11"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# lfifreak

> Automated LFI exploiter (Python 2) that tests PHP input, PHP filter and data-URI methods against one target and hands you bind or reverse shells for Windows and Linux.

## What it's for

LFiFreak (by Osanda Malith Jayathissa) exploits a known local file inclusion from an interactive menu. You give it the vulnerable URL (with the injectable parameter) and any cookies; it then either runs an automated test across all of its methods or executes a chosen one: `php://input` and `data://` for code execution, `php://filter` for base64 source disclosure of arbitrary files. Its distinctive feature is shell delivery: the exploit path integrates bind and reverse shell payloads for both Windows and Linux targets (it can fetch netcat from a URL you supply on the target's behalf), so the jump from inclusion to interactive shell is one flow. Python 2 era, single target per run. Siblings in this vault: [[lfi-exploiter]] (environ/log chain), [[lfi-fuzzploit]] (menu fuzz/exploit), [[lfi-sploiter]] (parameter scanner+dumper), [[lfi-image-helper]] (image payload crafting).

## Typical use cases

### Test all attack methods at once

Let it find what the inclusion supports.

```
lfifreak
# URL prompt: http://target.example.com/lfi.php?page=
# cookie prompt: (enter if none) -> menu choice 1 (Automated testing)
```

Outcome: prints `[*] Target is vulnerable to:` followed by the methods that worked (`phpInput`, `phpFilter`, `dataURI`) — the triage step that tells you which dedicated menu option to drive next.

### Read source via the PHP filter method

Base64 file disclosure without execution.

```
lfifreak
# URL prompt: http://target.example.com/lfi.php?page= -> menu choice 3 (PHP filter method)
# file path prompt: index.php
```

Outcome: requests the file through `php://filter` with base64 encoding and prints the encoded source — decode it locally to read configuration files, credentials and application logic that the web server would otherwise execute instead of display.

### Execute code and catch a shell

PHP input or data-URI execution with built-in shells.

```
lfifreak
# URL prompt: http://target.example.com/lfi.php?page= -> menu choice 2 (PHP input method)
# command prompt: e.g. whoami; the tool's shell flow can fetch netcat from a URL
# and offers bind/reverse shells for Windows and Linux targets
```

Outcome: the chosen method executes your command through the inclusion and prints the response; continuing the shell flow spawns the bind or reverse shell payloads (set up a listener on ATTACKER_IP first for reverse mode), giving an interactive shell on the target — the same flow works from menu choice 4 (Data URI method).

No corpus evidence — derived from upstream documentation and packaged source (OsandaMalith/LFiFreak README plus `lfi.py` menu/prompts read directly; BlackArch installs it as `/usr/bin/lfifreak` under Python 2).
