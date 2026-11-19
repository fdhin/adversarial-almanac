---
name: "snmp-brute"
categories:
  - blackarch-cracker
version: "19.830bb0a"
homepage: "https://github.com/SECFORCE/SNMP-Brute"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "SECFORCE/SNMP-Brute"
github-stars: 333
github-pushed: "2021-09-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snmp-brute

> SNMP v1/v2c community-string brute-forcer that pivots straight into enumeration and Cisco config extraction.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding valid SNMP community strings and exploiting them in one pass. The
script (SECFORCE's `snmpbrute.py`, installed as `snmpbrute`) blasts UDP
community-string guesses and simultaneously listens for any response, which
minimizes the wait per candidate. On a hit it distinguishes read-only from
read-write strings, enumerates the device (Windows, Linux or Cisco OID sets
via `snmpenum.pl`), attempts to download a Cisco router configuration through
a Metasploit module, reveals any plaintext passwords in that config, and runs
John the Ripper against hashed ones. It does not attack SNMPv3, which uses
authentication instead of community strings. Requires scapy plus the tools it
orchestrates: Metasploit, snmpwalk/snmpstat and John.

## Typical use cases

### Full brute force with automatic follow-on enumeration

Hand over one target and let it brute, enumerate and pillage.

```
snmpbrute -t 198.51.100.10
```

Outcome: brute forces community strings against the target, reports valid
(and read-write) strings found, then drops into an interactive enumeration
prompt; on Cisco devices it additionally attempts a config download and
password recovery.

### Brute force with a custom dictionary on a non-standard port

Point the guess list and port at a specific device.

```
snmpbrute -t 198.51.100.10 -f communities.txt -p 1161
```

Outcome: sends each community string from `communities.txt` to UDP port 1161
(default 161) and prints which strings drew a response, with `--rate`,
`--timeout` and `--delay` available to tune send pace and how long it waits
for UDP replies.

### Non-interactive brute-only pass

Skip the enumeration stage, e.g. for scripted sweeps over many hosts.

```
snmpbrute -t 198.51.100.10 -b -a -v
```

Outcome: with `--bruteonly` it stops after the community-string guesses
instead of enumerating, `--auto` removes the interactive prompts, and
`--verbose` prints per-attempt detail; combine with `--iplist hosts.txt` to
run the same pass across a file of targets.

### Validate a single community string

Check one known or guessed string without a dictionary.

```
snmpbrute -t 198.51.100.10 -c public
```

Outcome: tests only the supplied community string (`public`) against the
target and reports whether it is accepted and whether it grants read-write
access; `--stdin` similarly reads candidates from standard input instead of a
file.
