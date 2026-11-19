---
name: "rpcapd"
gentoo-category: "net-analyzer"
version: "4.1.2"
homepage: "http://www.winpcap.org/devel.htm"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo, aur]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "ExtraHop/rpcapd"
github-stars: 29
github-pushed: "2013-11-13"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rpcapd

> Remote packet-capture daemon from the libpcap/WinPcap family — serves a host's interfaces to remote libpcap clients over the rpcap protocol.

## What it's for

rpcapd is the daemon half of remote capture in the tcpdump ecosystem: clients
such as tcpdump, Wireshark or anything else built on libpcap can capture on a
machine where they are not installed, by speaking the rpcap protocol to rpcapd
and selecting one of its interfaces (with optional BPF filters pushed down to
the daemon). It runs in passive mode, where the client connects to rpcapd, or
active mode, where rpcapd dials out to the capture client — the answer for
targets behind NAT or firewalls. The daemon originates in the WinPcap project
(the vault's 4.1.2 lineage, still packaged by Pentoo; a buildable standalone
copy lives in the ExtraHop GitHub repo, which adds vendor-specific bulk
forwarding), while current libpcap releases maintain rpcapd as part of the
tcpdump-group source tree and Arch's AUR packages it from there — flags below
are documented by the tcpdump.org rpcapd(8) man page and are shared across
these lineages. Note rpcapd is a capture-forwarding service, not an assessment
tool: exposing it without authentication hands raw traffic to anyone who can
reach it.

## Typical use cases

### Listen for remote capture clients

Start the daemon so a remote workstation can capture this host's traffic.

```
rpcapd -b 0.0.0.0 -p 2002
```

Outcome: rpcapd listens on port 2002 (the default) for rpcap connections; on
the client side, Wireshark or `tcpdump -i rpcap://198.51.100.10:2002/eth0` can
now capture `eth0` remotely, with the daemon requesting authentication unless
`-n` is also given.

### Allow unauthenticated capture from specific hosts only

Open null authentication but restrict who may use it.

```
rpcapd -n -l 198.51.100.10,198.51.100.11
```

Outcome: connections are accepted without credentials (`-n`, "permit NULL
authentication") but only from the comma-separated host list (`-l`); every
other source is refused — the documented pairing for labs and monitoring
appliance setups where the rpcap protocol's own auth is not in play.

### Dial out to the capture client (active mode)

Reach a target-side daemon that cannot accept inbound connections.

```
rpcapd -a 198.51.100.10,2002
```

Outcome: rpcapd initiates the TCP connection to the capture client at
`198.51.100.10` port 2002 itself; once established, the session behaves like a
passive one, letting the client select interfaces and start capture — add `-d`
to detach into daemon mode on UNIX.

### Persist a configuration

Write the current flags to a config file for reuse.

```
rpcapd -b 127.0.0.1 -p 2002 -l 198.51.100.10 -n -s /etc/rpcapd.ini
```

Outcome: saves the current options into `/etc/rpcapd.ini` in
rpcapd-config(5) format (`-s`); later invocations can load it wholesale with
`rpcapd -f /etc/rpcapd.ini` and ignore command-line flags, and the UNIX daemon
re-reads it on SIGHUP. If built with TLS support, `-S` with `-K`/`-X` key and
certificate arguments enables encrypted, TLS-required sessions.

*No corpus evidence — derived from upstream documentation (tcpdump.org
rpcapd(8) man page, the ExtraHop rpcapd README, and the AUR/Pentoo packaging).*
