---
name: "netkit-rusers"
categories:
  - blackarch-recon
version: "0.17"
homepage: "https://packages.debian.org/source/sid/net/netkit-rusers"
packaged-by: [blackarch]
blackarch-builddate: "2021-02-07"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# netkit-rusers

> Lists who is logged in on machines on the local network, via the RPC rusers service.

## What it's for

netkit-rusers (0.17) provides `rusers`, the client for the classic rpc.rusersd service: it produces output like `who`, but for remote machines. Called with no host argument it broadcasts to the local network and reports every host running rusersd along with the usernames currently logged in there; with host arguments it queries only those machines. It waits up to one minute to catch late responders, so a broadcast sweep is deliberately slow. This is username reconnaissance over a legacy RPC service — informative where rusersd is still enabled (old UNIX estates, lab networks), but rusersd is disabled by default on modern systems and exposing it leaks exactly the user-activity data an attacker wants. The sibling tools [[netkit-rwho]] and [[netkit-bsd-finger]] enumerate the same era of disclosure-prone host-status services.

## Typical use cases

### Sweep the local network for logged-in users

Discover which machines answer and who is on them.

```
rusers
```

Outcome: broadcasts an rusers RPC request; each responding host prints one line of `hostname: user user user ...`, building a network-wide picture of active sessions. Expect to wait about a minute for stragglers before it gives up.

### Long, who-style output for specific hosts

Get per-terminal detail instead of a name list.

```
rusers -l target.example.com
```

Outcome: one `who`-format line per login session on the named host (user, tty/line, login time), showing not just who is on but on which terminals and since when.

### Include hosts with no users

Map every rusersd-equipped machine, not just busy ones.

```
rusers -a
```

Outcome: all responding machines are listed even when nobody is logged in, turning the query into a service/asset census of rusersd hosts on the segment.

*No corpus evidence — derived from upstream documentation (rusers(1) manpage from the packaged netkit-rusers 0.17 source).*
