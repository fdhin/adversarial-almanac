---
name: "climber"
categories:
  - blackarch-scanner
version: "30.5530a78"
homepage: "https://github.com/raffaele-forte/climber"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "raffaele-forte/climber"
github-stars: 125
github-pushed: "2016-03-31"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# climber

> Audits UNIX/Linux hosts over SSH or telnet for misconfigurations that allow local privilege escalation.

## What it's for

Climber (Raffaele Forte, v1.1) connects to a remote UNIX/Linux system with given credentials — password or private key — and runs a plugin-based audit for the classic local privilege-escalation classes: misconfigured sudo rules, weak permissions on sensitive files and similar misconfigurations, grouped into categories. A subset of the audit can be selected per run with `--category` or `--plugin`. Findings are printed during the run and rendered into a collapsible HTML report written under ~/climber. It is remote and credential-driven, not a script you copy to the target and run locally; it is a python2-era tool.

No corpus evidence — derived from upstream source.

## Typical use cases

### Audit a host over SSH

Run the full plugin set with password login.

```
climber --ssh --host target --port 22 --username user --password Password1
```

Outcome: connects over SSH, executes every audit plugin, prints findings to the console and writes an HTML report (open ~/climber/index.html) grouped by category and plugin.

### Audit over telnet

Run the same checks against legacy gear.

```
climber --telnet --host 198.51.100.10 --port 23 --username user --password Password1
```

Outcome: the identical plugin audit runs over a telnet session; the HTML report is written under ~/climber as above.

### Audit with a private key

Use key-based SSH authentication.

```
climber --ssh --host target --username user --privatekey ~/.ssh/id_rsa --passphrase Password1
```

Outcome: authenticates with the given key (passphrase optional; `--keytype` selects the key format) and runs the same category/plugin audit.
