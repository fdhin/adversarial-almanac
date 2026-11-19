---
name: "scannerl"
categories:
  - blackarch-fingerprint
version: "76.8133065"
homepage: "https://github.com/kudelskisecurity/scannerl"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-02-07"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kudelskisecurity/scannerl"
github-stars: 220
github-pushed: "2018-08-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scannerl

> Distributed, modular fingerprinting engine (Erlang) that fans out protocol probes across a master/slave cluster at internet scale.

## What it's for

Scannerl fingerprints thousands of targets in parallel using swappable probe modules — HTTP/HTTPS server headers, TLS certificates (HTTPS, IMAP, POP3, SMTP STARTTLS), SSH host keys, MySQL greeting version, Modbus, MQTT/MQTT-over-TLS, BACnet, FOX, chargen amplification — and swappable output modules (stdout, CSV, file, IP-only variants). It runs standalone, but always spawns a local slave node over SSH, so the host must resolve its own hostname and accept its own SSH key; distributed scans spread the same requirements across the fleet plus same-version Erlang everywhere, a shared Erlang cookie, and TCP/4369 with the 11100+ port range open. Distribution spreads both load and source IPs, so wide fingerprinting campaigns are less likely to be blacklisted than from one host. It identifies services on ports you point it at; it is not a port scanner.

## Typical use cases

### List fingerprinting and output modules

```
scannerl -l
```

Outcome: prints every available fingerprinting module with its port and purpose (e.g. `httpbg TCP/80: HTTP Server header identification`, `ssh_host_key TCP/22: SSH host key graber`) followed by the output modules (stdout, csv, file, …).

### Grab HTTP server headers for a domain

```
scannerl -m httpbg -d example.com
```

Outcome: starts a local slave automatically, fingerprints the resolved host on TCP/80 and prints one result per target with the `Server` header value; `-m httpbg:true` follows redirects.

### Fingerprint a target list from a file

```
scannerl -m httpbg -F targets.txt
```

Outcome: fingerprints every target listed one per line (IPs, hostnames or ranges), splitting large ranges into /24 chunks by default (`-c` changes the subdivision).

### Distribute a scan across slave nodes

```
scannerl -m ssh_host_key -f 198.51.100.0/24 -s host1,host2,host3
```

Outcome: the master SSHes into host1–host3, deploys Erlang slave nodes, splits the /24 between them and prints SSH host keys as they are collected; a multiplier like `host1*4` runs four slaves on one host.

### Fingerprint a service on a non-default port

```
scannerl -m mysql_greeting -p 13306 -f 198.51.100.10
```

Outcome: probes TCP/13306 with the MySQL greeting probe and reports the server version extracted from the initial handshake packet.

### Save results through an output module

```
scannerl -m httpbg -d example.com -o file:/tmp/result
```

Outcome: routes every `{module, target, port, result}` tuple to the `file` output module writing `/tmp/result` instead of stdout; module arguments are appended after a colon.

### Measure a chargen amplification factor

```
scannerl -m chargen -f 198.51.100.10
```

Outcome: sends UDP/19 chargen probes and reports each target's amplification factor — the request-to-response size multiplier that determines how usable the host is as a reflection amplifier.

*No corpus evidence — derived from upstream documentation.*
