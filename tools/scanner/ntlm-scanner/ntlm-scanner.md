---
name: "ntlm-scanner"
categories:
  - blackarch-scanner
version: "6.4b29329"
homepage: "https://github.com/preempt/ntlm-scanner"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "preempt/ntlm-scanner"
github-stars: 201
github-pushed: "2020-11-08"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ntlm-scanner

> Preempt's Impacket-based checker that authenticates over SMB with deliberately malformed NTLM to test servers for known NTLM protocol weaknesses.

## What it's for

This is Preempt's NTLM vulnerability scanner (published alongside their Drop-the-Mic and related NTLM research): it connects to a target's SMB service and performs an otherwise-valid NTLM authentication with one field deliberately broken per the tested vulnerability. If the server accepts it, the host is vulnerable to that specific NTLM attack (message-integrity bypass, key disclosure, mailbox MIC removal and friends) and the corresponding relay/attack becomes possible. Scans use valid login material and mostly generate no failed logins — except CVE-2019-1338, which does produce failed authentications and can trigger account lockout, so check the flag before running it on production. It is an audit tool for defenders and pentesters verifying patch state, not an exploit. Requires a recent impacket; upstream entry point is `scan.py` (distributions may wrap it under the package name).

## Typical use cases

### Test one host for one vulnerability

```
python3 scan.py -vuln CVE-2019-1040 -target 'user:Password1@198.51.100.10'
```

Outcome: authenticates over SMB to the host with the CVE-2019-1040-specific malformed NTLM (missing MIC) and prints whether the server accepted it — accepted means the host is vulnerable and NTLM relay attacks like mailbox MIC-removal apply. Target format is `[domain/]user[:password]@<host>`.

### Scan a list of hosts

```
python3 scan.py -vuln CVE-2019-1166 -target-file targets.txt
```

Outcome: runs the selected check against every host in the file and reports per-host verdicts — the batch form for auditing a domain's patch state.

### Authenticate with hashes instead of a password

```
python3 scan.py -vuln CVE-2019-1040 -target 'user@198.51.100.10' -hashes LMHASH:NTHASH
```

Outcome: performs the same test using pass-the-hash style credentials (LM:NT format), for environments where plaintext is not available.

### Check domain controllers for actual ZeroLogon exploitation

```
python3 scan.py -vuln CVE-2020-1472 -target-file dc-list.txt
```

Outcome: this mode works differently — it repeatedly attempts an empty-password authentication against the listed DCs to detect *actual exploitation* of CVE-2020-1472 (a compromised DC's machine-account password becomes empty), rather than mere presence of the flaw.

*No corpus evidence — derived from upstream documentation (README usage and embedded `scan.py -h` output of preempt/ntlm-scanner).*
