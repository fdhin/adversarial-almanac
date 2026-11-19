---
name: "yasuo"
categories:
  - blackarch-webapp
version: "121.994dcb1"
homepage: "https://github.com/0xsauby/yasuo"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0xsauby/yasuo"
github-stars: 572
github-pushed: "2017-12-09"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# yasuo

> Ruby scanner that sweeps a network for third-party web applications with publicly known exploits.

## What it's for

Yasuo (by 0xsauby) scans networks for vulnerable and exploitable third-party web applications — the Apache Tomcat manager, JBoss jmx-console, Jenkins/Hudson and thousands of other front-ends that carry remotely exploitable, publicly documented vulnerabilities. It either port-scans the targets itself or consumes existing nmap XML output, probes each web service (including a canary request to a non-existent path to filter soft 404s), and fingerprints what it finds against a signature file (`signatures.yaml`, replaceable with your own). For applications with authentication it can brute-force form-based and/or HTTP basic auth. Output is the list of identified — and thus likely pwnable — applications. A Ruby tool; the distro package wraps `yasuo.rb` as the `yasuo` command.

## Typical use cases

### Port-scan a range and enumerate vulnerable apps

```
yasuo -r 198.51.100.0/24 -p 80,443,8080,8443
```

Outcome: Yasuo nmap-scans the range on the given ports, fingerprints every web service found against its signatures, and prints the vulnerable applications it recognized.

### Work from an existing nmap scan, with auth brute-force

```
yasuo -f nmap_scan.xml -b all
```

Outcome: the nmap XML output is parsed instead of scanning again, and every discovered application with a login gets both its form and HTTP basic authentication brute-forced (`-b form` or `-b basic` to limit).

### Scan a host list with custom signatures

```
yasuo -l hosts.txt -p 80,443 -s my_signatures.yaml
```

Outcome: hosts are read one per line, scanned on the given ports, and fingerprinted against your custom signature file instead of the default one.

### Re-run enumeration without redoing discovery

```
yasuo -u savedURLstate12345.out -s my_signatures.yaml
```

Outcome: Yasuo reuses the "good URLs" saved by a previous run (`savedURLstate*.out`) and immediately re-enumerates applications against them — useful for testing an updated signature set without re-scanning.

*No corpus evidence — derived from upstream documentation (README of 0xsauby/yasuo).*
