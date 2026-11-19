---
name: "tnscmd10g"
categories:
  - database
  - web
version: "1.3-1kali3"
homepage: "http://www.red-database-security.com/"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "hacktrackgnulinux/tnscmd10g"
github-stars: 1
github-pushed: "2022-12-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tnscmd10g

> Send raw TNS commands to an Oracle database listener without an Oracle client.

*No corpus evidence — derived from upstream documentation.*

## What it's for

tnscmd10g is a small Perl script that speaks Oracle's TNS wire protocol to
the listener service (tnslsnr, TCP port 1521 by default), letting you issue
listener commands — ping, version, status, services and friends — with no
Oracle client installed. During authorized assessments it is a quick way to
enumerate an exposed Oracle listener: version banners, running instances
and service handlers leak out of the responses. It is a 2000-2005-era
tool (jwa's tnscmd extended for Oracle 10g); it only converses with the
listener, never authenticates to the database, and cannot run SQL.

## Typical use cases

### Check that a listener answers

Confirm TNS reachability before deeper probing.

```
tnscmd10g ping -h target.example.com
```

Outcome: builds a TNS packet carrying a `ping` command, sends it to port
1521, and prints the listener's decoded raw response — establishes that a
listener is alive and responding (ping is the default command when none is
given).

### Fingerprint the listener version

Grab the banner for version-matching.

```
tnscmd10g version -h target.example.com
```

Outcome: the response contains the listener's TNS version information —
protocol version and platform/Oracle release strings — the fastest way to
identify an Oracle listener's generation.

### Ask the listener for status

Enumerate instances and handlers.

```
tnscmd10g status -h target.example.com
```

Outcome: the status response typically lists the instance(s) started, their
SIDs/service handlers, and listener uptime — the main information leak this
tool is used for.

### Talk to an Oracle 10g listener

Use the extended command block 10g expects.

```
tnscmd10g --10G status -h target.example.com
```

Outcome: sends the fuller `CONNECT_DATA` structure (service, arguments,
version fields) instead of the minimal legacy string, so 10g listeners
answer commands they would otherwise refuse.

### Send a hand-built TNS command

Cover listener commands outside the canned set, on any port.

```
tnscmd10g --rawcmd '(CONNECT_DATA=(COMMAND=services))' -h target.example.com -p 1522
```

Outcome: transmits the arbitrary `CONNECT_DATA` string verbatim inside a
TNS packet (`services` here) to the chosen port, printing the raw reply —
useful for commands the script has no shorthand for, or listeners moved
off 1521. Running `tnscmd10g` with no `-h` prints the full usage summary.
