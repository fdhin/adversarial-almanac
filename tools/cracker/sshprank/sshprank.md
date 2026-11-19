---
name: "sshprank"
categories:
  - blackarch-cracker
version: "1.7.0"
homepage: "https://nullsecurity.net/tools/cracker.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "noptrix/sshprank"
github-stars: 179
github-pushed: "2026-05-03"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# sshprank

> High-volume SSH mass-scanner and login cracker that sources its targets from lists, masscan, random IPv4 generation, or Shodan.

## What it's for

sshprank (noptrix / nullsecurity.net, Python) finds SSH servers at scale and tests logins against them. Targets can be a host list, CIDR or IP range, the result of an embedded masscan port scan, randomly generated IPv4 addresses, or a Shodan query; it then cracks with single credentials, user/password lists, or user:pass combo files across separate thread pools for hosts, services, and logins. Around the cracking core it can grab sshd banners, check whether servers still allow password authentication, execute a command on cracked hosts, spoof its client version string, and save/resume session state. Found logins land in `owned.txt` as `host:port:user:pass` lines. Intended for auditing large estates for weak SSH credentials; by nature it is loud.

## Typical use cases

### Crack a list of SSH servers

Point it at a prepared target list with credentials from files.

```
sshprank -h sshds.txt -U user -P passes.txt -x 20
```

Outcome: 20 parallel host threads try each user/password pair (`sshds.txt` accepts hosts, CIDRs, ranges, or `host:port` entries); found logins print and append to `owned.txt` (override with `-o`) in `host:port:user:pass` format.

### Port-scan with masscan, then crack

Let an embedded masscan sweep find the sshd services first.

```
sudo sshprank -m '-p22 --rate 5000 --range 198.51.100.0/24' -P Password1
```

Outcome: masscan (its options passed through verbatim) locates open SSH ports in the range; the discovered services are saved to `sshds.txt` in `-h`-compatible format and immediately cracked with the given password.

### Source targets from Shodan

Skip scanning entirely by querying Shodan for SSH servers.

```
sshprank -s 'SSH;1;50'
```

Outcome: queries Shodan for up to 50 SSH servers (query;page;limit — a larger limit needs your own API key) and cracks logins against what comes back using the default `root:root` credentials.

### Grab banners or test password-auth support

Recon modes that do not attempt logins.

```
sshprank -b hosts.txt > sshds.txt
sshprank -p sshds.txt -v
```

Outcome: `-b` writes each target's sshd version banner in the exact format `-h` accepts (a chainable discovery step); `-p` reports for each server whether password authentication is still permitted, so cracking effort can skip key-only hosts.

### Random brute force

Spread attempts so no single host sees a rapid sequence.

```
sshprank -h sshds.txt -U users.txt -P passes.txt -Z 500
```

Outcome: 500 attempts, each against a randomly chosen target with a randomly chosen credential pair (`0` runs infinitely until Ctrl+C); `-C 'uptime'` would additionally execute a command on each cracked host.

*No corpus evidence — derived from upstream documentation.*
