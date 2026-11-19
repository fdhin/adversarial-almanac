---
name: "eternal-scanner"
categories:
  - blackarch-scanner
version: "101.3979b0f"
homepage: "https://github.com/peterpt/eternal_scanner"
packaged-by: [blackarch]
blackarch-builddate: "2024-08-04"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "peterpt/eternal_scanner"
github-stars: 340
github-pushed: "2024-07-31"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# eternal-scanner

> Two-stage MS17-010 sweep by peterpt: masscan finds hosts with TCP/445 open, Metasploit confirms which are actually vulnerable to EternalBlue (CVE-2017-0144), and the hits are collected in a file.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Eternal Scanner (command `escan`, with its log helper `elog`) automates finding Windows machines vulnerable to the EternalBlue SMBv1 exploit (CVE-2017-0144) across an IP or CIDR range. It refuses to run without root, masscan, metasploit-framework and wget, and it only accepts IPs or IP ranges — hostnames are rejected. It deliberately does not exploit anything: the pipeline is masscan → 445 open → Metasploit MS17-010 check, with confirmed hosts written to `vuln.txt` in the data directory. It can also re-test an existing list against EternalRomance (CVE-2017-0145, named-pipe variant) and search previously accumulated results. Scanning addresses you do not maintain is illegal in most jurisdictions; the tool is aimed at administrators verifying their own networks.

## Typical use cases

### Sweep a network for EternalBlue-vulnerable hosts

The plain launch form takes an IP or range and auto-sets the scan rate to 500 packets/second.

```
sudo escan 198.51.100.0/24
```

Outcome: masscan sweeps the range for TCP/445 while a live counter of open hosts prints, then Metasploit checks each SMB host for MS17-010; start/stop timestamps are shown and confirmed-vulnerable IPs are appended to `vuln.txt`.

### Scan one IP at a chosen rate

Rate and target are combined with `-i` (the `-s` rate switch must follow the IP, e.g. `-i <ip> -s <rate>`).

```
sudo escan -i 198.51.100.10 -s 10000
```

Outcome: the same pipeline against a single host with masscan raised to 10000 pk/s (upstream advises staying at or below 10000 on a 100 Mbit link); results again land in `vuln.txt`.

### Re-check a supplied IP list

Feed a file with one IP per line instead of a range.

```
sudo escan -ck iplist.txt
```

Outcome: every listed IP goes through the vulnerability check and newly confirmed hits are merged into `vuln.txt` — the way to revalidate stale results or audit a third-party list. (`escan -c` re-checks only the IPs already in `vuln.txt`.)

### Test found hosts for EternalRomance

Once a vulnerable list exists, probe it for the CVE-2017-0145 SMBv1 named-pipe variant.

```
sudo escan -er
```

Outcome: each IP in the current vulnerable list is checked for EternalRomance with per-host verdicts (Vulnerable / Not Vulnerable / Time Out / Offline) and a result file is written under the tool's data directory.

### Review what has been collected

No new scanning — dump the accumulated findings.

```
escan -vo
```

Outcome: prints the detailed vulnerable-IP list with per-host OS notes; `escan -l` instead prompts for a search term against the stored database. Use `escan -h` for the full switch list and `escan -u` to pull updated binaries from upstream.
