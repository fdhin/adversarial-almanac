---
name: "sshatter"
categories:
  - blackarch-cracker
version: "1.2"
homepage: "http://www.nth-dimension.org.uk/downloads.php?id=34"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "CiscoCXSecurity/SSHatter"
github-stars: 2
github-pushed: "2017-09-04"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sshatter

> Parallel SSH login brute-forcer that keeps working after the crack — command execution and file transfer across every host it opens.

## What it's for

SSHatter (Tim Brown, Nth Dimension; Perl) brute-forces SSH logins in parallel across a list of targets using a password list, a directory of private keys, or "dumb" combinations (username as password, the username alone, blank). Its distinguishing feature is the follow-on phase: every working credential found is immediately usable to run one command on all cracked hosts at once (mass mode), drive them interactively, or upload and download files. It implements SSH purely in Perl via Net::SSH::Perl, so no local ssh binary is required. It is a horizontal credential-testing tool for hosts you are authorized to assess, not an offline key-password recovery aid.

## Typical use cases

### Brute-force with user and password lists

Crack many targets in parallel with dictionaries.

```
sshatter -x 10 -t targets.txt -u users.txt -p passwords.txt
```

Outcome: up to 10 parallel workers try every user/password combination against every target (`targets.txt` holds one `host[:port]` per line); cracked pairs are reported as they are found and can be written to a file with `-X found.txt`.

### Dumb mode without any lists

Check for the classic lazy combinations.

```
sshatter -x 10 -t targets.txt -u users.txt -d
```

Outcome: for each username it tries the username itself as password, the bare username, and a blank password — a fast default-credential sweep producing the same reporting as full mode.

### Run a command on every cracked host

Combine the crack with a mass command.

```
sshatter -x 10 -t targets.txt -u users.txt -p passwords.txt -m 'uname -a'
```

Outcome: after the crack phase the command runs on every host where credentials were found and its output is aggregated in the console; add `-0` to echo the cracked password to stdin for sudo -S, or `-s` to be prompted before each execution.

### Resume post-exploitation without re-cracking

Reuse a previous run's results.

```
sshatter -I found.txt -m 'id'
```

Outcome: imports the login pairs previously exported with `-X` and goes straight to the command-execution phase against those hosts, skipping the brute-force work entirely.

*No corpus evidence — derived from upstream documentation.*
