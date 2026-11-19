---
name: "logmepwn"
categories:
  - blackarch-scanner
version: "24.f257a8f"
homepage: "https://github.com/0xInfection/LogMePwn"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-21"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0xInfection/LogMePwn"
github-stars: 397
github-pushed: "2026-10-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# logmepwn

> Fast mass scanner and validator for Log4Shell (CVE-2021-44228) with canary-token callbacks.

## What it's for

LogMePwn (upstream binary `lmp`, packaged as `logmepwn`) sweeps many hosts for the Log4J RCE CVE-2021-44228 across HTTP, IMAP, SSH and FTP, injecting JNDI payloads built around a canary token — auto-generated via canarytokens.org, or pointed at your own callback server — so that a callback proves the injection actually executed rather than merely echoing a scanner string. Multi-threaded Go: targets come from the command line, a file, or CIDR ranges; HTTP methods, headers and body formats (JSON, XML, custom) are configurable. It validates exposure — the callback tells you where injection worked; it does not deliver a shell.

## Typical use cases

### Scan hosts with an emailed canary

Get per-target verdicts plus email proof of execution.

```
logmepwn -email alerts@example.com 198.51.100.10 198.51.100.20:8080
```

Outcome: registers a canary token, fuzzes the default HTTP/IMAP/SSH/FTP ports on both targets with JNDI payloads in many header positions, and prints per-target status; an email from the canary service confirms any host that rendered the payload.

### Sweep a target list with webhook callbacks

Feed callbacks into your own tooling.

```
logmepwn -webhook https://webhook.example.com/hook -file targets.txt
```

Outcome: scans every entry in `targets.txt` (host, `host:port`, or CIDR per line) and delivers callback notifications to the webhook instead of email.

### Target one protocol with a custom callback server

Test a specific surface with your own listener.

```
logmepwn -protocol ssh -custom-server callback.example.com 198.51.100.10:22
```

Outcome: runs only the SSH plugin against port 22 with payloads pointing at your callback server — omit `-protocol` to run all protocol plugins (http, imap, ssh, ftp) on their default ports.
