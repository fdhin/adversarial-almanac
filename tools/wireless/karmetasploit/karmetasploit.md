---
name: "karmetasploit"
gentoo-category: "net-wireless"
version: "0.1-r3"
homepage: "http://dev.metasploit.com/redmine/projects/framework/wiki/Karmetasploit"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# karmetasploit

> KARMA-style rogue access point attack driven by a Metasploit resource file.

## What it's for

Karmetasploit packages the KARMA concept (answer every probe so nearby
clients auto-join your access point) as a Metasploit workflow: a
launcher script brings up a soft access point with airbase-ng, attaches
DHCP, redirects traffic, and starts Metasploit loading `/etc/karma.rc` —
a resource file that spins up credential-capture servers and a
browser-exploit module against whoever connects. It dates from the
Metasploit 3 era (the packaged launcher warns that it leaves the system
in a modified state and that leftover monitor interfaces, dhcpd and
airbase-ng may need manual cleanup). The idea was later superseded by
purpose-built rogue-AP stacks — see [[karma]] for the original 2006
toolkit, and [[hostapd-mana]] / [[mana]] for the maintained successors.

## Typical use cases

### Launch the full fake access point

Bring up the complete Karmetasploit stack on one wireless interface.

```
karmeta.sh wlan0
```

Outcome: puts wlan0 into monitor mode, starts an airbase-ng access
point named "Free Wifi" on channel 6, configures the at0 interface as
10.0.0.1/24, starts dhcpd from `/etc/karmeta-dhcpd.conf`, redirects
traffic with iptables, and opens the Metasploit console loading
`/etc/karma.rc` — clients that connect are served the capture modules
and recorded in the Metasploit database (`/root/karma.db`).

### Run just the Metasploit side

Load the KARMA service set inside an existing Metasploit session.

```
msfconsole -r /etc/karma.rc
```

Outcome: Metasploit loads the resource file, creates its sqlite
database and starts the auxiliary/server/capture listeners (POP3 on 110
and 995, IMAP on 143 and 993, FTP, plus the browser_autopwn handler on
port 55550) — useful when the access-point side is already handled by
another tool.

*No corpus evidence — derived from upstream documentation.*
