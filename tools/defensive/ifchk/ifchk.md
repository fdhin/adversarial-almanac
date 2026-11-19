---
name: "ifchk"
categories:
  - blackarch-defensive
version: "1.1.2"
homepage: "http://www.noorg.org/ifchk/"
packaged-by: [blackarch]
blackarch-builddate: "2021-01-31"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ifchk

> Daemon that watches a host's network interfaces and reports — or automatically disables — any that enter promiscuous mode.

## What it's for

ifchk (Interface Check, by Joshua Birnbaum of Noorg, Inc.) scans a host's configured interfaces and reports their state — normal, PROMISC, *down* — alongside interface traffic metrics. Promiscuous mode is the mark of a sniffer, so ifchk provides host-based detection of packet capture on the machine itself, while the metrics reports help spot traffic spikes consistent with someone dumping traffic off the wire. It runs as a daemon by default, reporting through syslog, or in the foreground with `-F`; with the `disableOnPromisc` config directive it will administratively shut down any promiscuous interface it finds. It targets Linux, developed and tested from kernel 2.6 through current RHEL/CentOS releases.

## Typical use cases

### Take a one-shot reading

Print the current interface report without daemonizing.

```
sudo ifchk -F
```

Outcome: runs in the foreground and prints the interface status report — each interface with its PROMISC / normal / *down* state — plus traffic metrics, then exits; any interface shown as PROMISC is processing all link traffic and likely has a sniffer attached.

### Monitor continuously as a daemon

Keep promiscuous-mode detection running on a server.

```
sudo ifchk
```

Outcome: detaches and rescans interfaces at the configured interval (ifStatusDumpFreq), reporting status and metrics through syslog; an interface flipping to PROMISC shows up in the daemon's log.

### Disable promiscuous interfaces automatically

Turn detection into immediate containment.

```
# /etc/ifchk/ifchk.conf
disableOnPromisc        1
errLog                  /var/log/ifchk.errlog
ifStatusDumpFreq        3600

sudo ifchk -f /etc/ifchk/ifchk.conf
```

Outcome: the daemon starts with directives from the given absolute path; any interface found in promiscuous mode is reported as `PROMISC [*]` and administratively disabled — the same effect as marking it down — until an administrator brings it back up.

### Check the build matches the host

Confirm version and runtime environment.

```
ifchk -v
```

Outcome: prints the program revision, compile date and the host's OS, kernel and hardware details — a quick sanity check that the binary matches the machine it runs on.

*No corpus evidence — derived from upstream documentation.*
