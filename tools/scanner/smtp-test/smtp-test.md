---
name: "smtp-test"
categories:
  - blackarch-scanner
version: "6.151f636"
homepage: "https://github.com/isaudits/smtp-test"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-10"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "isaudits/smtp-test"
github-stars: 14
github-pushed: "2025-10-23"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smtp-test

> One-command SMTP assessment: nmap script scans, MX/mail-server checks and inbound-spoofing tests against a single mail server.

## What it's for

smtp-test.py (Matthew C. Jones, IS Audits & Consulting) automates SMTP server testing for penetration tests. Given an organization's mail server it runs nmap SMTP script scans, performs Mxtoolbox-style mail server checks, and tests whether mail that appears to come from inside the organization can be delivered — both from an external SMTP server (spam filters should reject outside mail with internal From addresses) and directly against the target server (which should require authentication). The external spoof leg needs an SMTP relay under the assessor's control, configured in the script's `config/config.cfg`. It is an assessment harness around nmap and DNS/MX lookups, not a brute-forcer or a relay scanner for whole IP ranges.

## Typical use cases

### Run the full assessment

```
smtp-test mail.example.com
```

Outcome: executes the complete sequence — nmap script scans against the server, mail-server checks, and both spoof tests — printing per-check results to the console so each control can be marked pass/fail.

### Test inbound spoofing with explicit addresses

```
smtp-test mail.example.com -f ceo@example.com -t it-support@example.com
```

Outcome: attempts to deliver a message that appears sent from `ceo@example.com` to `it-support@example.com`; if either the external-relay or the direct-to-target attempt succeeds, inbound spoofing controls are missing and the successful delivery path is reported.

### Direct tests only, no mail sent

```
smtp-test --no-spoof mail.example.com
```

Outcome: skips both relay/spoof attempts and runs only the nmap script scans and server-side checks — for engagements where sending mail is out of scope. The mirror option `--no-tests` does the opposite: only the spoofing leg runs.

*No corpus evidence — derived from upstream documentation.*
