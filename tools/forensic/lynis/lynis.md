---
name: "lynis"
categories:
  - forensics
  - vulnerability
version: "3.1.7-1"
homepage: "https://cisofy.com/lynis/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-06-26"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "CISOfy/lynis"
github-stars: 16650
github-pushed: "2026-09-16"
github-archived: false
github-issues: 246
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 62
gh-activity-checked: "2026-11-09"
---

# lynis

> Security auditing and hardening tool for Unix-based systems, developed by CISOfy.

## What it's for

Lynis runs an in-depth security audit on the host it executes on — typically as root — checking system hardening, vulnerable software, and configuration weaknesses. It prints warnings and concrete hardening suggestions per test and finishes with a hardening index. It serves defensive use (system administrators, auditors, compliance testing such as ISO27001, PCI-DSS, HIPAA) and offensive use (pentesters checking a foothold for configuration issues and privilege-escalation leads). It is an assessment tool, not an exploit framework: it reports findings, it does not attack. The open-source edition is GPLv3; CISOfy sells a commercial enterprise edition with reporting on top.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Audit the local system as root

Run the full local test battery.

```
sudo lynis audit system
```

Outcome: performs hundreds of tests, prints warnings and suggestions with references, ends with a hardening index, and writes a log and a machine-readable report (/var/log/lynis.log and /var/log/lynis-report.dat) for later review.

### Quick non-privileged triage

Points of interest without root, e.g. from an unprivileged shell.

```
lynis audit system --pentest
```

Outcome: runs non-privileged tests only and shows points of interest for pentesting instead of the full hardening audit.

### Forensic mode on a running or mounted system

```
sudo lynis audit system --forensics
```

Outcome: performs forensics-oriented checks on a running or mounted system, rather than the standard hardening scan.

### Audit a Dockerfile

```
lynis audit dockerfile Dockerfile
```

Outcome: analyzes the Dockerfile and reports security issues in the image build instructions.

### Audit a remote host over SSH

```
lynis audit system remote user@target.example.com
```

Outcome: connects to the remote host over SSH and runs the system audit there, collecting the results on the local machine.
