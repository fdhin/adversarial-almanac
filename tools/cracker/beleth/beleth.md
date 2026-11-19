---
name: "beleth"
categories:
  - blackarch-cracker
version: "36.0963699"
homepage: "https://github.com/chokepoint/Beleth"
packaged-by: [blackarch]
blackarch-builddate: "2014-09-02"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "chokepoint/Beleth"
github-stars: 93
github-pushed: "2014-02-04"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# beleth

> Multi-threaded, dictionary-based online SSH password cracker written in C on libssh2.

## What it's for

Beleth performs online password guessing against SSH servers: given a target, a username and a wordlist, it spawns a configurable number of threads and tries each password until authentication succeeds. It is meant for testing the strength of SSH credentials on systems you are authorized to test — every attempt is a real login, so it is noisy in auth logs and can trip lockout policies. It is not an offline cracker and has no hash or keyfile mode. On success it can optionally execute a command on the remote host.

## Typical use cases

### Run a threaded wordlist attack against one account

```
beleth -t 198.51.100.10 -u root -w wordlist.txt -l 15
```

Outcome: reads the wordlist, spawns 15 threads against root@198.51.100.10:22, and prints `[*] Authentication succeeded (root:<password>@198.51.100.10:22)` on a hit.

### Verify a single candidate password

```
beleth -t target.example.com -u admin -P 'Winter2024!'
```

Outcome: makes one authentication attempt with the literal password and reports success or failure immediately.

### Attack a non-standard port and run a command on success

```
beleth -t target.example.com -p 2222 -u deploy -w /usr/share/beleth/wordlist.txt -c 'id'
```

Outcome: attacks port 2222 and, on a successful login, executes `id` on the remote host and prints its output; add `-v` to log every attempt or `-vv` for debug detail.

*No corpus evidence — derived from upstream documentation.*
