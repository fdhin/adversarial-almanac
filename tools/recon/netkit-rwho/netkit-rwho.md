---
name: "netkit-rwho"
categories:
  - blackarch-recon
version: "0.17"
homepage: "http://packages.qa.debian.org/n/netkit-rwho.html"
packaged-by: [blackarch]
blackarch-builddate: "2021-02-07"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "aur-archive/netkit-rwho"
github-stars: 0
github-pushed: "2015-08-16"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netkit-rwho

> Remote who: shows who is logged in on local-network machines from the rwhod status broadcasts.

## What it's for

netkit-rwho (0.17, with Debian patches) packages the classic `rwho` client and the `rwhod` daemon. Each host running rwhod periodically broadcasts a UDP status packet naming its logged-in users; `rwho` collates those broadcasts (read from `/var/spool/rwho/whod.*`) and prints a network-wide `who`. A host that has not reported for eleven minutes is assumed down and dropped from the output, and users idle for an hour or more are hidden unless `-a` is given. From the offensive side, `rwho` is free username and session reconnaissance wherever rwhod is left enabled; from the defensive side, rwhod itself is the leak — running it broadcasts your user list to the whole segment, so it is normally disabled. The same family of legacy host-status enumeration is covered by [[netkit-rusers]] and [[netkit-bsd-finger]].

## Typical use cases

### List logged-in users across the local network

See who is on which machine segment-wide.

```
rwho
```

Outcome: one line per remote login (`user  tty  host  login-time  idle`), assembled from the rwhod broadcasts received on this segment; hosts silent for 11 minutes and users idle over an hour are omitted.

### Include long-idle users

Surface sessions that the default output hides.

```
rwho -a
```

Outcome: the same listing but with users idle for an hour or more included — useful when auditing whether forgotten/idle administrative sessions are hanging around on monitored hosts.

*No corpus evidence — derived from upstream documentation (rwho(1) manpage from the packaged netkit-rwho 0.17 source; rwhod behavior from the same package).*
