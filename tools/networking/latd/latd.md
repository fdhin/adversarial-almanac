---
name: "latd"
categories:
  - blackarch-networking
version: "1.31"
homepage: "http://sourceforge.net/projects/linux-decnet/files/latd/1.31/"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "pld-linux/latd"
github-stars: 1
github-pushed: "2025-07-14"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# latd

> User-space LAT terminal daemon that lets Linux and BSD hosts take part in DEC's Local Area Transport network.

## What it's for

latd is a clean-room implementation of DEC's LAT protocol as a user-space daemon — no kernel DECnet support or patching required (it uses packet sockets on Linux, BPF on BSD). It advertises login services that DECservers and VMS/Tru64 hosts can connect to, connects out to advertised LAT services via reverse-LAT devices under `/dev/lat/`, and supports service ratings, group codes, queueing (for printer ports), and a node responder for small terminal servers. In a modern lab its value is interoperating with legacy DEC-era terminal servers and VMS hosts on a recovered network segment. It is controlled almost entirely through the `latcp` utility; running `latd` directly is explicitly unsupported — start it with `latcp -s`, which also executes `/etc/latd.conf` as a shell script for local customisation. The GitHub repository listed above is a distro packaging mirror; the real upstream is the SourceForge linux-decnet project.

## Typical use cases

### Start and stop the LAT daemon

```
sudo latcp -s
```

Outcome: starts `latd` the only supported way, running `/etc/latd.conf` first so configured services and ports come up; `latcp -h` halts the daemon, killing active sessions without warning.

### Advertise a login service to DECserver terminals

```
sudo latcp -A -a TERMLOGIN -i 'Linux login service'
```

Outcome: registers a LAT service named TERMLOGIN that appears in the service lists DECserver terminals build; anyone selecting it gets `/bin/login` on this host (override with `-C command`, run as another user with `-u`, and pin a static rating with `-s`). `latcp -D -a TERMLOGIN` removes it again.

### List the LAT services learned on the circuit

```
latcp -d -l
```

Outcome: dumps every service latd has learned from other LAT nodes, with their ratings — the quick way to see what a DECserver or VMS host is offering before connecting.

### Log into a remote LAT service interactively

```
llogin TERMLOGIN
```

Outcome: connects the terminal to the TERMLOGIN service via latd (pick a specific advertising node with `-H node`, a named port with `-R port`); the session ends with the default quit character CTRL-]. `llogin -d` alone re-lists learned services.

### Create a reverse-LAT device for a printer

```
sudo latcp -A -p /dev/lat/laser -V PRINTQUEUE -H decserver -Q
```

Outcome: creates a persistent `/dev/lat/laser` device backed by the PRINTQUEUE service on node decserver; writes to the device go out over LAT, and `-Q` makes connections queue while the printer is busy (required for DECserver 90L printer service).

*No corpus evidence — derived from upstream documentation.*
