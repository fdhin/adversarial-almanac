---
name: "apt2"
categories:
  - blackarch-automation
version: "v1.0.20180315.r12.g8075cdc"
homepage: "https://github.com/MooseDojo/apt2"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "MooseDojo/apt2"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# apt2

> Automated penetration testing toolkit that feeds Nmap/Nessus/Nexpose results into a knowledge base and chains safe-level-gated enumeration and exploit modules.

## What it's for

APT2 (Automated Penetration Testing Toolkit) runs an Nmap scan itself or imports existing Nmap, Nessus, or Nexpose XML results, stores everything it learns in a local Knowledge Base (KB), and then automatically launches enumeration and exploit modules that match the discovered services — for example MS17-010/MS08-067 checks, Tomcat manager logins, JexBoss, anonymous FTP/LDAP tests, SMB share searches, SSL scans, and hash dumping. Every module carries a safe level from 0 to 5 (5 = very safe) and APT2 only runs modules at or above the configured threshold, which is how you control how aggressive the automation is. It does not implement exploits itself: it orchestrates external tools (nmap, hydra, msfconsole, responder, smbclient, secretsdump.py, sslscan, john, and others) that must be present for the corresponding modules to work, and it can optionally drive Metasploit over its MSGRPC API. The original MooseDojo repository is gone and the code is Python 2, so treat it as legacy tooling; BlackArch packages a mirror of the final v1.0 code under the `apt2` command.

*No corpus evidence — derived from the archived upstream README (repo gone; read via the packaged mirror and the Wayback archive).*

## Typical use cases

### Start the interactive console

Open APT2's menu-driven mode where scan settings and targets can be configured at runtime.

```
apt2
```

Outcome: launches the interactive APT2 console (banner, KB initialization); from the menu you can configure Nmap target/type/port settings or load files and kick off the automated module chain.

### Import existing scan results

Reuse results from a scan you already ran instead of re-scanning.

```
apt2 -f nmap-scan.xml
```

Outcome: APT2 parses the XML file(s) (one or more, space separated; Nmap, Nessus, or Nexpose format), populates its knowledge base with hosts/services/vulnerabilities, and begins launching matching modules. Combine with a second filename to import multiple files at once.

### Scan a target range from scratch

Let APT2 do its own Nmap scan and then automate the follow-up work.

```
apt2 --target 198.51.100.0/24 --ip 198.51.100.10
```

Outcome: APT2 runs its configured Nmap scan against the range (use `--ip` to set your actual local IP, which some modules need) and then chains enumeration/exploit modules against whatever services are found, at the default safe level 4.

### Restrict automation to read-only modules

Load scan data without letting APT2 fire any active module at the targets.

```
apt2 -f nessus-scan.xml -x action
```

Outcome: the scan file is loaded into the knowledge base only; `-x` excludes the comma-separated module types (action, input, report) from running, so here every action module is skipped. Raise the bar further with `-s 5` to permit only very-safe modules when you do let it act.

### Review the module catalog before scanning

See what APT2 can launch and at which aggressiveness level.

```
apt2 --listmodules
```

Outcome: prints the full module table (module name, type, safe level, description) and exits — use it to pick a `--safelevel` threshold and to check which external tools each module depends on.
