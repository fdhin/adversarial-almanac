---
name: "mongoaudit"
categories:
  - blackarch-scanner
version: "222.70b83e8"
homepage: "https://github.com/stampery/mongoaudit"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "stampery/mongoaudit"
github-stars: 1327
github-pushed: "2023-04-17"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mongoaudit

> Console-based MongoDB auditor: configuration weaknesses, known CVEs and per-check fix advice in one interactive run.

## What it's for

mongoaudit (Stampery) is a terminal UI tool (urwid-based, no browser) that connects to a MongoDB server and runs roughly twenty checks: listening port and bind exposure, the HTTP status interface on 28017, version disclosure and currency, TLS, authentication and SCRAM-SHA-1, server-side JavaScript, plus a backlog of known MongoDB CVEs reaching back to 2012. Failed checks come with remediation guidance rather than just a red flag, and the advanced checks (user roles, per-database permissions) require valid credentials, which the interface prompts for. It audits one server per run — it is a configuration auditor, not a mass scanner and not an exploit tool.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Audit a MongoDB server interactively

Run the console auditor against a MongoDB instance.

```
mongoaudit
```

Outcome: the urwid interface opens with its basic and advanced test suites; enter the target server (URI, port, optional credentials) and the suite runs, ending in a per-check pass/fail view with fix advice for everything flagged. Press q to quit or ctrl+r to restart the app.
