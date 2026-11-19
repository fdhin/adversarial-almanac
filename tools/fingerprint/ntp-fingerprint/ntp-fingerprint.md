---
name: "ntp-fingerprint"
categories:
  - blackarch-fingerprint
version: "0.1"
homepage: "http://www.hackingciscoexposed.com/?link=tools"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-24"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ntp-fingerprint

> Minimal Perl probe that remotely identifies the operating system behind an NTP server from the system information its daemon discloses.

## What it's for

ntp-fingerprint sends a single NTP control query over UDP/123 to a timeserver and reads back the system variables the NTP daemon volunteers: daemon version string (e.g. `ntpd 4.2.0@...`), processor architecture and operating system name/version (e.g. `Linux/2.6.10`, `cisco`). Because many ntpd builds answer these queries by default, the reply leaks the host OS even when other fingerprinting avenues are filtered. It is a single-purpose 2005-era script (v0.1): one server per run, no sweeping, no output formats. Modern ntpd deployments often restrict control queries, in which case the run ends with a receive error. It reads system info only — it is not a monlist/amplification tool.

## Typical use cases

### Fingerprint a single NTP server

```
ntp-fingerprint -t target.example.com
```

Outcome: prints a banner block stating whether an NTP server was found at the host and, when the daemon discloses its system variables, the NTP daemon version, processor and operating system lines (e.g. `# Operating system: Linux/2.6.10-gentoo-r5`); a filtered or rate-limited daemon yields a receive error instead.

### Show usage

```
ntp-fingerprint -h
```

Outcome: prints the one-line usage message (`usage: perl ntp-fingerprint.pl -t <timeserver>`) and exits.

*No corpus evidence — derived from upstream documentation.*
