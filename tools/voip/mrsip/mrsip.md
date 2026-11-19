---
name: "mrsip"
categories:
  - blackarch-voip
version: "110.bdd98ad"
homepage: "https://github.com/meliht/mr.sip"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "meliht/mr.sip"
github-stars: 438
github-pushed: "2026-09-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# mrsip

> Console SIP audit tool: device scanning, user enumeration and DoS attack simulation in one Python package.

## What it's for

Mr.SIP (Ismail Melih Taş) is a SIP-based audit and attack tool that grew out of academic research into SIP DDoS attacks. It offers three mutually exclusive modules per invocation: SIP-NES scans networks for SIP devices and fingerprints manufacturer/product/version, SIP-ENUM identifies valid SIP users and their authentication requirements, and SIP-DAS simulates TDoS floods with a built-in IP-spoofing engine. The package wraps the Python script as `mrsip` (upstream runs `python3 mr.sip.py`). Note that `--mt=register` and `--mt=subscribe` change real server state — a successful REGISTER can knock a legitimate phone off its extension — so restrict those and all SIP-DAS runs to authorised targets.

## Typical use cases

### Scan a subnet for SIP devices

```
mrsip --nes --tn=198.51.100.0/24 --mt=options --from=1000 --to=2000
```

Outcome: multithreaded OPTIONS sweep (10 workers by default, `--tc` to change) of the /24 that accepts single IPs, ranges or CIDR; responding SIP devices with product/version details are reported and the live-host list is written to `output/ip_list.txt`, which SIP-ENUM reads by default.

### Enumerate valid SIP users

```
mrsip --enum --from=users.txt
```

Outcome: for each live target (taken from the NES output list, or name one with `--tn=198.51.100.10`) sends a SUBSCRIBE probe (`--mt` default) for every name in users.txt and reports which extensions exist and what authentication they require; add `--mt=register` to probe with real REGISTER requests instead.

### Simulate a spoofed INVITE flood

```
mrsip --das --mt=invite -c 10000 --tn=198.51.100.10 -r
```

Outcome: fire-and-forget INVITE flood of 10,000 packets (default SDP body included) at the single target IP, with randomly spoofed source addresses (`-r`; `-s` spoofs from the target's subnet, `-m` from a list file via `--il`); `--pps` caps the send rate and `-v` enables debug logging.

*No corpus evidence — derived from upstream documentation.*
