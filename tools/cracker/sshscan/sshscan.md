---
name: "sshscan"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "https://github.com/getdual/scripts-n-tools/blob/master/sshscan.py"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "getdual/scripts-n-tools"
github-stars: 88
github-pushed: "2026-02-25"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# sshscan

> Horizontal SSH scanner that pushes one configured credential pair across huge IPv4 lists and logs which hosts accept it.

## What it's for

A small Python 2 script (phxbandit) for the horizontal scenario: you have one username/password pair and want to know which hosts across a large IP range accept it — the mirror image of per-host brute-forcers. Targets come from `iplist.txt` in the working directory, mixing single IPs, CIDR blocks, and dash ranges freely; the order is randomized to spread load. Credentials are not command-line options: you edit the `USER` and `PASS` constants at the top of the script. For each host with port 22 open it drives the real ssh client via pexpect to run `uname -a` with the configured password. There is no option parsing at all — it is a script you configure and run, intended for sweeping ranges you are authorized to assess.

## Typical use cases

### Configure and run a sweep

Set the credential, fill the target list, run.

```
python2 -u sshscan.py
```

Before running, edit the script header (e.g. `USER = 'user'`, `PASS = 'Password1'`) and fill `iplist.txt` with entries like `198.51.100.10`, `198.51.100.0/24`, or `198.51.100.50-198.51.100.90`.

Outcome: prints per-host `Port: 22/open` or `closed` progress lines, attempts `ssh user@host uname -a` with the configured password on every open host, and records open hosts plus any command output in `sshscan-output-<timestamp>.txt`.

### Generate a range list from a GeoIP CSV

The script header documents building `iplist.txt` from a Maxmind country database.

```
grep -i Japan GeoIPCountryWhois.csv | awk -F, '{print $1"-"$2}' | sed -e 's/"//g' > iplist.txt
```

Outcome: converts each country block's start and end IPs into `start-end` range lines sshscan understands, producing a country-sized target list ready for a sweep.

*No corpus evidence — derived from upstream documentation.*
