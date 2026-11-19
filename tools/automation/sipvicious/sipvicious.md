---
name: "sipvicious"
categories:
  - blackarch-automation
version: "481.485d16c"
homepage: "http://blog.sipvicious.org/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-07-19"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "EnableSecurity/sipvicious"
github-stars: 1113
github-pushed: "2026-07-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 18
gh-activity-checked: "2026-11-09"
---

# sipvicious

> SIP/VoIP audit suite: find SIP servers, enumerate extensions, crack their passwords.

## What it's for

SIPVicious (EnableSecurity) audits SIP-based VoIP systems in three stages:
which hosts speak SIP (svmap), which extension lines exist on a PBX and
which of them require authentication (svwar), and what password protects a
given extension (svcrack, via digest authentication). Scan sessions can be
saved, resumed and later exported to PDF, XML, CSV or text (svreport). It
speaks SIP over UDP/TCP (and IPv6 with `-6`) — it is not a general VoIP or
web scanner. Current releases install the console scripts as
`sipvicious_svmap`, `sipvicious_svwar`, `sipvicious_svcrack`;
older releases and some distros expose the same tools as bare `svmap`,
`svwar`, `svcrack`, and the BlackArch package hyphenates them to
`sipvicious-svmap` style.

## Typical use cases

### Discover SIP servers across a network range

```
sipvicious_svmap 198.51.100.10-198.51.100.20 198.51.100.0/24
```

Outcome: probes the range and prints every host that answers as a SIP
device, with its SIP user agent string. Use `-p5060,5061,8000-8100` for
non-default ports, `-m INVITE` to make phones ring, or `-6 -p 5060
[2001:db8::10]` for IPv6.

### Enumerate working extensions on a PBX

```
sipvicious_svwar -e100-999 198.51.100.10
```

Outcome: tries to register each extension in the range and reports which
ones exist and whether they require authentication. A word list of
extension names works too (`-d dictionary.txt`); `-m OPTIONS` or
`-m INVITE` switches the probe method for PBXs that hide extensions from
REGISTER.

### Crack an extension's password

```
sipvicious_svcrack -u100 -d passwords.txt udp://198.51.100.10:5080
```

Outcome: runs digest-authentication password guesses against extension 100
and prints the password on success. For numeric PIN-style passwords use a
range instead: `-u100 -r1-9999 -z4` tries 0001 through 9999 with zero
padding.

### Save a session and review it later

```
sipvicious_svwar -s session1 -e0-99999 -z5 198.51.100.10
sipvicious_svreport list
```

Outcome: the extension scan is checkpointed under the name `session1`
(resume later with `--resume session1`), and `svreport` lists saved
sessions from all sipvicious tools, which can then be exported for
reporting.

*No corpus evidence — derived from upstream documentation.*
