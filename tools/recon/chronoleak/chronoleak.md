---
name: "chronoleak"
categories:
  - blackarch-recon
version: "0.0.1"
homepage: "https://github.com/noraj/ChronoLeak"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/ChronoLeak"
github-stars: 5
github-pushed: "2026-11-02"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 34
gh-activity-checked: "2026-11-09"
---

# chronoleak

> Leak a remote host's clock through ICMP timestamp requests — unauthenticated and rarely filtered.

## What it's for

ChronoLeak (a Ruby gem by noraj) discloses the current time on a remote machine by sending an ICMP timestamp request (type 13) and decoding the timestamp reply (type 14) — the classic Nessus "ICMP Timestamp Request Remote Date Disclosure" / CVE-1999-0524 issue. Knowing the target's real clock matters for time-sensitive attacks: TOTP codes are only valid in 30-second windows, and tokens or certificates can appear invalid purely because of clock drift. The tool shells out to `hping3`, which must be installed and runnable with sudo; if no reply comes back it reports that ICMP timestamp traffic is filtered. Some Windows versions deliberately falsify the timestamp by up to 1000 seconds as a mitigation.

## Typical use cases

### Leak a target's remote time

Read the clock of a host before time-sensitive work.

```
chronoleak 198.51.100.10
```

Outcome: sends one ICMP timestamp request via hping3 (`sudo hping3 <target> --icmp --icmp-ts -c 1`) and prints the remote time decoded from the reply's transmit timestamp; use it to calibrate TOTP windows or token validity windows against the target's actual clock.

### Show usage and references

Confirm the interface and the underlying issue.

```
chronoleak
```

Outcome: with no argument (or `-h`/`--help`) prints the one-line usage `chronoleak <IP_address_or_domain>`, the version, and links to the Nessus plugin and CVE-1999-0524 write-up.

*No corpus evidence — derived from upstream source (bin/chronoleak).*
