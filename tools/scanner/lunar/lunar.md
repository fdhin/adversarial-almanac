---
name: "lunar"
categories:
  - blackarch-scanner
version: "1280.1d78b365"
homepage: "https://github.com/lateralblast/lunar"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lateralblast/lunar"
github-stars: 317
github-pushed: "2026-10-02"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 418
gh-activity-checked: "2026-11-09"
---

# lunar

> CIS-framework security audit — and optional lockdown — for Unix hosts and cloud accounts.

## What it's for

Lunar (Lockdown UNix Auditing and Reporting, upstream script `lunar.sh`) scores a system against the CIS benchmarks and several other frameworks: Linux distributions, Solaris, macOS, FreeBSD, AIX and ESXi get an OS hardening audit, with additional audit modes for Docker, Kubernetes, AWS and Azure (read-only, via the respective CLIs). Findings include suggested fix commands, and each recommendation can be emitted as an Ansible stanza. It can also apply a lockdown, but the author recommends audit mode only — treat lockdown (`-l`) as at-your-own-risk with backups.

## Typical use cases

### Audit the local OS

Score the host without changing it.

```
lunar -a
```

Outcome: runs the CIS-based test set and prints a scored pass/warn/fail report; `-A` additionally includes filesystem checks. No changes are made to the system.

### Audit a Docker or Kubernetes installation

Check the container stack the same way.

```
lunar -d
```

Outcome: audits Docker daemon configuration and reports the same scored findings; `-k` runs the Kubernetes audit instead.

### Emit Ansible remediation snippets

Turn findings into configuration management.

```
lunar -a -n
```

Outcome: the audit report is accompanied by example Ansible stanzas implementing each recommendation, ready to be moved into a role.
