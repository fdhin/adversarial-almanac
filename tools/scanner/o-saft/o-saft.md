---
name: "o-saft"
categories:
  - blackarch-scanner
version: "7174.dbf462bc"
homepage: "https://www.owasp.org/index.php/O-Saft"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "OWASP/O-Saft"
github-stars: 389
github-pushed: "2026-10-07"
github-archived: false
github-issues: 12
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# o-saft

> OWASP SSL advanced forensic tool: a Perl scanner that reports a target's certificate details and probes its SSL/TLS configuration and cipher acceptance independently of the local crypto libraries.

## What it's for

O-Saft answers "what exactly does this TLS service accept?" — certificate details and chain, protocol versions, cipher suites (it can check up to 65535 ciphers per protocol regardless of what the local OpenSSL build supports), and configuration checks (weak ciphers, missing/too-long keys, BEAST/CRIME-class settings, renegotiation and more via its `+check` commands). Its design goals are repeatability and independence: results do not depend on the installed library versions, it works in closed networks without internet access, and it needs only core Perl for basic operation. It is read-only reconnaissance and audit — it never sends attacks. Usage is command-oriented (`+info`, `+check`, `+cipher`, `+quick`, plus `--help=commands` for the full list); the package ships `o-saft.pl` and wrapper `o-saft`/GUI `o-saft.tcl`.

## Typical use cases

### Full configuration check

```
o-saft.pl +check target.example.com
```

Outcome: connects, then reports certificate and protocol findings as rated checks (good/insufficient/warning per item) — the compliance-style overview of what the TLS service gets wrong.

### Dump certificate and connection info

```
o-saft.pl +info target.example.com
```

Outcome: prints the structured facts first — issuer/subject/validity/SANs, chain, protocol versions offered, and the locally-visible parameters — before any judgment, ideal as evidence in a report.

### Enumerate accepted ciphers

```
o-saft.pl +cipher target.example.com
```

Outcome: probes cipher acceptance (independent of the local OpenSSL's cipher table) and lists which suites the service accepts or rejects, including the weak/legacy ones that nmap- or openssl-based checks miss.

### Quick overview

```
o-saft.pl +quick target.example.com
```

Outcome: the condensed one-screen summary of the most relevant findings — the triage pass before running the fuller `+check`/`+cipher` commands.

### Discover the command surface

```
o-saft.pl --help=commands
o-saft.tcl
```

Outcome: `--help=commands` lists every `+command` and option (there are hundreds); `o-saft.tcl` opens the simple Tk GUI for interactive checks, and the plain `o-saft` wrapper picks between CLI, GUI and Docker automatically.

*No corpus evidence — derived from upstream documentation (README quick start and description of OWASP/O-Saft).*
