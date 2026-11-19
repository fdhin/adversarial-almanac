---
name: "rww-attack"
categories:
  - blackarch-webapp
version: "0.9.2"
homepage: "http://packetstormsecurity.com/files/79021/Remote-Web-Workplace-Attack-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rww-attack

> Dictionary attack against the Remote Web Workplace portal of Windows Small Business Server 2003/2008.

## What it's for

RWW-Attack (mikey27, distributed via Packet Storm) performs online password
guessing against Microsoft Windows Small Business Server's Remote Web
Workplace (RWW) portal — the forms login remote workers use. It runs five
parallel login threads and takes a user list plus a password list. Its
distinguishing feature is lockout awareness: it defaults to the SBS 2003
policy (45 invalid attempts per user, then a 10-minute cooldown) and lets you
set your own threshold and duration so a run does not lock out real accounts.
It only tests the RWW form — it does not attack other SBS services or
exploit anything. The tool is python2/pycurl-era software from 2010, of use
against legacy SBS 2003/2008 networks still found in old environments.

## Typical use cases

### Run a lockout-safe dictionary attack

Supply host, user list and password list.

```
rww-attack -t 198.51.100.10 -u users.txt -p passwords.txt
```

Outcome: attempts each password for each user over the RWW portal with five
threads and prints valid credentials as they are found; supply at least five
passwords (the tool pads shorter lists for its hardcoded threads). Running
it twice is recommended to confirm results.

### Target SBS 2008 and save hits to a file

The 2008 portal ("cougar") needs a flag and a longer attack window.

```
rww-attack -t 198.51.100.10 -u users.txt -p passwords.txt -c -o found.txt
```

Outcome: switches to the SBS 2008 login flow (notably slower) and appends
every successful user/password pair to found.txt; note the `administrator`
account is disabled by default on SBS 2008.

### Match a stricter account-lockout policy

Stay under a known lockout threshold instead of the SBS 2003 defaults.

```
rww-attack -t 198.51.100.10 -u users.txt -p passwords.txt -l 5 -d 15
```

Outcome: tries at most 5 passwords per user, then sleeps 15 minutes before
the next batch, avoiding lockouts on hosts with an aggressive policy; an
invalid `-l`/`-d` choice will lock users out exactly like client-side
failures would.

*No corpus evidence — derived from packaged source (Packet Storm RWW-Attack 0.9.2 zip README and usage()/getopt in the script itself; installed as `/usr/bin/rww-attack`).*
