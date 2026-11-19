---
name: "changeme"
categories:
  - blackarch-scanner
version: "272.26fa2c3"
homepage: "https://github.com/ztgrace/changeme"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ztgrace/changeme"
github-stars: 1514
github-pushed: "2025-07-08"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# changeme

> Default and backdoor credential scanner for common network services and web applications.

## What it's for

changeme detects default and backdoor credentials — the logins shipped on devices, appliances and web apps — rather than guessed common passwords. Its default mode scans HTTP/HTTPS, and it also speaks mssql, mysql, postgres, ssh (password or key), snmp, mongodb and ftp. Credential data lives in YAML files kept separate from the code, so new defaults can be added without writing modules (`--mkcred` scaffolds one interactively, `--dump` lists what is built in). Targets are a single host, a subnet, a host list, an nmap XML file or a Shodan query, with the type inferred automatically. The telnet scanner is broken upstream, and it does no password guessing beyond the known-default lists.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scan a subnet for default credentials

Check every host in a range for known default logins.

```
changeme 198.51.100.0/24
```

Outcome: multi-threaded scan of the subnet that reports each valid default credential found (service, host, username/password), ending with a summary of confirmed defaults.

### Restrict a scan to one product's credentials

Run only the credential sets that name a given product and tune the timeout.

```
changeme -n "Apache Tomcat" --timeout 5 198.51.100.0/24
```

Outcome: only credentials matching "Apache Tomcat" are tried, each connection gets 5 seconds, and valid Tomcat default logins are reported.

### Scan from an nmap XML file

Feed prior port-discovery results straight in.

```
changeme subnet.xml
```

Outcome: changeme parses the nmap XML, infers the open services and checks only those instead of re-discovering the subnet.

### Check SSH passwords and known keys

Audit SSH separately for default passwords and published private keys.

```
changeme --protocols ssh,ssh_key 198.51.100.0/24
```

Outcome: SSH hosts are checked against default password lists and bundled well-known key pairs; matches are reported per host.

### Use Shodan as the target source

Pull a target list from a Shodan query instead of scanning locally.

```
changeme --shodan_query "Server: SQ-WEBCAM" --shodan_key keygoeshere -c camera
```

Outcome: hosts matching the Shodan query are collected and checked against the camera category's default credentials.

### Target one service with URI syntax

Hit a single host/protocol pair directly.

```
changeme snmp://198.51.100.20
```

Outcome: only SNMP on that host is probed with the built-in default community strings.

### Inventory or extend the credential database

List every default the scanner can check, or create a new one.

```
changeme --dump
changeme --mkcred
```

Outcome: `--dump` prints all built-in credential sets by category and protocol; `--mkcred` asks a few questions and writes a new YAML credential file you can drop in.
