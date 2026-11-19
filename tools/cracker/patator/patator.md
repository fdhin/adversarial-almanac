---
name: "patator"
categories:
  - blackarch-cracker
version: "1.1.0.r4.g964e87c"
homepage: "https://github.com/lanjelot/patator"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-01-15"
build-system: python
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "lanjelot/patator"
github-stars: 3941
github-pushed: "2026-11-07"
github-archived: false
github-issues: 41
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# patator

> Multi-protocol bruteforcer with key=value module options, wordlist keywords and response-driven actions.

## What it's for

patator is a multi-threaded Python bruteforcer written as a reaction to Hydra/Medusa-style tools: every target protocol is a module — ssh_login, ftp_login, http_fuzz, smb_login, mssql_login, mysql_login, pgsql_login, ldap_login, vnc_login, snmp_login, telnet_login, rdp_login, dns_forward, unzip_pass and ~25 more — taking `key=value` options instead of a fixed flag soup. Candidate values are injected with keywords: FILE0/FILE1 (wordlists), COMBO00/COMBO01 (user:pass combo files), NET0 (host ranges/subnets), RANGE0 and MOD0 (numeric and enumerated values). Global options act on each response: `-x` conditions mark failures to ignore, retry or skip; `-t` sets thread count; `--rate-limit` and `--timeout` temper speed; `-l`/`--hits`/`--xml` log runs; `--resume` restarts an interrupted session from its printed offsets. It is a dictionary-attack workhorse for login pages and network services — not an offline hash cracker.

## Typical use cases

### Brute-force an SSH login

Try a password list against one account.

```
patator ssh_login host=198.51.100.10 user=root password=FILE0 0=passwords.txt -x ignore:mesg='Authentication failed.'
```

Outcome: attempts every password from passwords.txt (10 threads by default) and prints a code/size/time table in which responses matching the ignore condition are suppressed; a valid credential appears as a hit with a success message, and the closing line reports Hits/Done/Skip/Fail with the average rate.

### Attack a web login form (http_fuzz)

Guess the password on a POST login page, filtering out the generic failure page.

```
patator http_fuzz url=http://198.51.100.10/index.php method=POST body='name=admin&password=FILE0' 0=passwords.txt follow=1 accept_cookie=1 -x ignore:fgrep='Login name or password is incorrect.'
```

Outcome: posts each candidate with redirects followed and cookies carried, hides responses containing the failure string, and reports the rest with HTTP status and content length — the module also supports `before_urls` to fetch a session/CSRF token first, and COMBO00/COMBO01 files for user:password pairs.

### Cross user and password files (ftp_login)

Test the cartesian product of accounts and passwords.

```
patator ftp_login host=198.51.100.10 user=FILE0 0=users.txt password=FILE1 1=passwords.txt -x ignore:mesg='Login incorrect.' -x ignore,reset,retry:code=500
```

Outcome: iterates every users.txt × passwords.txt combination; the first `-x` hides plain authentication failures while the second ignores, resets and retries on FTP code 500 so a flaky server does not burn candidates. Successes surface as code 230 rows flagged `Login successful.`

### Recover an encrypted ZIP's password

Offline dictionary attack on a file — no network target involved.

```
patator unzip_pass zipfile=encrypted.zip password=FILE0 0=passwords.txt -x ignore:code!=0
```

Outcome: tries each list entry against the archive and reports only candidates whose exit code is 0 (the correct password); being offline, it runs at list speed with nothing to lock out.

### Throttle and resume a long run

Keep a big attack survivable and restartable.

```
patator ssh_login host=198.51.100.10 user=FILE0 0=users.txt password=FILE1 1=passwords.txt -t 4 --rate-limit 2 -l /tmp/patator-run
```

Outcome: runs with 4 threads and at most one attempt every 2 seconds (lockout-friendly), saving output and response data under /tmp/patator-run; when a session is interrupted, re-run the same command with `--resume <offsets>` using the offsets patator prints at exit to continue exactly where it stopped.
