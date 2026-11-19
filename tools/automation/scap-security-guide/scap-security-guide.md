---
name: "scap-security-guide"
categories:
  - blackarch-automation
version: "0.1.80"
homepage: "https://www.open-scap.org/security-policies/scap-security-guide/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-04-29"
build-system: scripts
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "ComplianceAsCode/content"
github-stars: 2835
github-pushed: "2026-11-09"
github-archived: false
github-issues: 246
github-match: "search"
gh-checked: "2026-11-09"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2841
gh-activity-checked: "2026-11-09"
---

# scap-security-guide

> The ComplianceAsCode project (formerly SCAP Security Guide): the de facto open-source library of hardening and audit content — SCAP data streams, Ansible playbooks, Bash scripts and Kubernetes CEL checks — for RHEL, Fedora, Ubuntu, Debian, SLES and more.

## What it's for

This is a defensive compliance artifact, not a program: what you install is a corpus of security baseline content (profiles like PCI-DSS, CIS, DISA STIG, OSPP) plus the data streams and remediation scripts generated from it. You consume it with scanners and automation you already have — `oscap`/`oscap-ssh` to audit a machine against a data stream, Ansible playbooks or Bash scripts to remediate it into compliance, and the compliance-operator's CEL content for Kubernetes/OpenShift. It contains no attack functionality at all; on an offensive engagement its value is reading: the profiles are a machine-readable catalog of exactly which settings a hardened host should have, which cuts both audit and hardening review time. Content lands in `/usr/share/xml/scap/ssg/content/` (data streams) and `/usr/share/scap-security-guide/` (ansible/, bash/). Distros package it under this name or as `ssg-*`; the upstream project renamed itself ComplianceAsCode in 2018, and the release ZIPs ship the same data streams for use on any machine.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### List what profiles a data stream offers

```
oscap info /usr/share/xml/scap/ssg/content/ssg-rhel8-ds.xml
```

Outcome: prints the data stream's metadata — its ID, schema version, checks/fixes present, and the full list of profile IDs (`xccdf_org.ssgproject.content_profile_ospp`, `..._pci-dss`, `..._stig`, ...) — so you can pick the exact baseline string to pass to an evaluation.

### Audit a host against a profile

```
sudo oscap xccdf eval --profile xccdf_org.ssgproject.content_profile_ospp --results-arf arf.xml --report report.html --oval-results /usr/share/xml/scap/ssg/content/ssg-rhel8-ds.xml
```

Outcome: evaluates every rule in the OSPP profile against the local machine, printing pass/fail per rule, and writes `arf.xml` (full machine-readable results in ARF format, reusable for later comparison) plus `report.html` (a human-readable report to open in a browser). Swap the profile ID and data-stream file for your platform/baseline pair.

### Audit a remote machine over SSH

```
oscap-ssh root@198.51.100.10 22 xccdf eval --profile xccdf_org.ssgproject.content_profile_standard --results-arf arf.xml --report report.html /usr/share/xml/scap/ssg/content/ssg-fedora-ds.xml
```

Outcome: copies the needed content to the remote host over SSH, runs the evaluation there with the local machine's data stream, and pulls the ARF results and HTML report back — `oscap` must exist on the remote host, the SSG content does not.

### Remediate to compliance with a generated Ansible playbook

```
ansible-playbook -i "localhost," -c local /usr/share/scap-security-guide/ansible/rhel9-playbook-ospp.yml
```

Outcome: applies every fix in the generated OSPP playbook to the local machine — this changes system configuration, so run it on a host you intend to harden, not one you only want to audit (`ls /usr/share/scap-security-guide/ansible/` lists the available playbooks; `check-mode` with `--check` evaluates without changing). The Bash equivalents live in `/usr/share/scap-security-guide/bash/` (e.g. `rhel8-script-pci-dss.sh`) for deployment scenarios where Ansible is not an option.
