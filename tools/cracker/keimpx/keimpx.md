---
name: "keimpx"
categories:
  - blackarch-cracker
version: "300.37190f4"
homepage: "http://code.google.com/p/keimpx/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "nccgroup/keimpx"
github-stars: 268
github-pushed: "2024-01-05"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# keimpx

> Validates dumped credentials against many SMB hosts, then drops you into an SMB shell.

## What it's for

keimpx answers the post-exploitation question "where else do these credentials work?" Given user/password pairs, NTLM hashes, or pass-the-hash style logon tokens, it sprays them across a list of Windows/SMB hosts and reports which combinations are valid where. After the checking phase it can hand you an interactive SMB shell on any host you picked: browse/upload/download shares, list users, domains and password policy, and deploy/undeploy services. It is a credential-validation and pivoting tool — it does not crack the credentials themselves, and it is not a scanner that discovers SMB hosts for you (you supply targets). Reach for it right after dumping hashes on one box to sweep an entire range without cracking anything.

## Typical use cases

### Sweep a dumped NTLM hash across a subnet

Check where a single user's hash authenticates, without cracking it.

```
keimpx -l targets.txt -U user --nt <nthash> -D CORP -T 20 -b
```

Outcome: connects to every host in `targets.txt` (one IP per line; a single host can be given with `-t 198.51.100.10`) and attempts an SMB login with the user and NT hash in parallel; valid combinations are reported per host. `-b` (batch mode) skips the interactive shell prompt so the run finishes on its own — leave it off to continue into a shell on a hit. `--lm <lmhash>` is accepted the same way for LM hashes.

### Test a whole credentials file against a host list

Feed combinations harvested from a compromise.

```
keimpx -l targets.txt -c creds.txt
```

Outcome: every `user:password` line in `creds.txt` (or `user:hash:...` forms) is tested against every target; when a valid login is found, keimpx asks which host and credential pair to use and opens an interactive SMB shell — from there you can list/browse/download/upload files in shares, enumerate users and password policy, and spawn a command prompt or deploy a service.

### Run a command list everywhere valid credentials exist

Execute the same commands against all reachable hosts in one pass.

```
keimpx -l targets.txt -c creds.txt -b -x commands.txt
```

Outcome: after validating credentials, runs each command from `commands.txt` via SMB against every host where the credentials worked and prints the per-host output — a quick way to inventory many machines without opening shells one by one.

*No corpus evidence — derived from upstream documentation.*
