---
name: "bluebox-ng"
categories:
  - blackarch-voip
version: "1.1.0"
homepage: "https://github.com/jesusprubio/bluebox-ng"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: node
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "jesusprubio/bluebox-ng"
github-stars: 264
github-pushed: "2026-03-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluebox-ng

> Node.js pentesting framework focused on VoIP/UC — interactive console that drives SIP scanning and bruteforcing, Asterisk AMI post-exploitation, protocol bruteforce and OSINT modules.

## What it's for

bluebox-ng (Jesús Prubio) is a UC/VoIP security testing framework presented as a Metasploit-style console: you pick a module by name, it prompts for its options with defaults, and results come back with report generation. SIP coverage is the core — host/port scanning over UDP, TCP, TLS and websockets (RFC 7118), extension and credential bruteforcing through different request types, SIP Torture (RFC 4475) checks, SQLi probes, unauthenticated-call tests and DoS testing — plus Asterisk AMI post-exploitation (config retrieval, dialplan inspection, command execution) and bruteforce modules for SSH, MySQL, MongoDB, LDAP, SNMP, (S)FTP, HTTP and Asterisk AMI itself. OSINT helpers include SHODAN searches, Google dorks for VoIP gear, CVE/OSVDB/Exploit-DB lookup and geolocation. The packaged version is 1.1.0, the last JavaScript-console release (the project later moved toward an auto-driven engine); its Node dependencies are dated, so expect install friction on current Node.

## Typical use cases

### Enter the console and discover modules

```
bluebox-ng
```

Outcome: starts the interactive client at the `Bluebox-ng>` prompt with TAB completion for module names; `help` lists every module with a one-line description and `help sipScan` prints a module's option set (types, defaults) as JSON before you run it.

### Scan a SIP infrastructure

```
Bluebox-ng> sipScan
* targets: Hosts to explore (127.0.0.1): 198.51.100.10
* transport: Underlying protocol (UDP):
* ports: Ports to explore (5060):
* meth: Type of SIP packets to do the requests (OPTIONS):
```

Outcome: pressing Enter accepts each shown default, so only the target needs typing; the module sends OPTIONS requests and reports live SIP hosts, their ports and responding transports (udp/tcp/tls/ws) — the starting point for every other SIP module.

### Reuse one target across modules

```
Bluebox-ng> setg
* variable: targets
* value: 198.51.100.10
```

Outcome: stores `targets` as a global variable (listed by `getg`) so subsequent modules offer 198.51.100.10 as the default instead of 127.0.0.1 — no re-typing between scan, bruteforce and torture modules.

### Brute-force SIP extensions and credentials

```
Bluebox-ng> sipBruteExt100
Bluebox-ng> sipBrute
```

Outcome: `sipBruteExt100` (and its `sipBruteExt404` variant) enumerate valid extensions via REGISTER handling, then `sipBrute` runs credential attacks through different SIP request types against the discovered extensions; findings feed directly into registration-hijack or call-fraud testing.

### Let the framework run a full UC assessment

```
Bluebox-ng> autoVoip
```

Outcome: the automated VoIP/UC sequence chains discovery and the standard SIP checks against the given target and collects the results for the report generator — the one-command pass to run when profiling an unknown PBX, with individual modules reserved for follow-up on anything it flags.

*No corpus evidence — derived from upstream documentation.*
