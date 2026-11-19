---
name: "acccheck"
categories:
  - blackarch-cracker
version: "0.2.1"
homepage: "http://labs.portcullis.co.uk/tools/acccheck/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/acccheck"
github-stars: 23
github-pushed: "2018-01-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# acccheck

> Dictionary attacks against Windows SMB logins from a single compact Perl script.

## What it's for

acccheck (Portcullis Labs) tests Windows accounts over the SMB protocol with wordlists: give it targets, usernames and passwords and it tries every combination over the network, reporting which pairs authenticate. With no user supplied it defaults to the local Administrator account; with no password it tries a blank one, so a bare `-t` run is a fast "is Administrator password-less?" check. It is purely online guessing — it does not crack captured hashes — and is most effective against old, unhardened Windows hosts; newer hosts may lock accounts after failed attempts, so check the lockout policy before running large lists.

## Typical use cases

### Check for a blank Administrator password

One-shot test of the default account on a single host.

```
acccheck -t 198.51.100.10
```

Outcome: attempts a blank password against the target's Administrator account and prints the result; a reported success means the account has no password set.

### Try a password list against a named user

```
acccheck -t 198.51.100.10 -u administrator -P passwords.txt
```

Outcome: attempts every password in the file against the named account and prints each combination that authenticates.

### Sweep targets, users and passwords verbosely

```
acccheck -T targets.txt -U users.txt -P passwords.txt -v
```

Outcome: works through the full target × user × password set in verbose mode, printing per-attempt status so successful logins stand out.

*No corpus evidence — derived from upstream documentation.*
