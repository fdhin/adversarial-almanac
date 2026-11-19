---
name: "rdesktop-brute"
categories:
  - blackarch-cracker
version: "1.5.0"
homepage: "http://www.rdesktop.org/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-07"
build-system: autotools
license: GPL-3.0-only
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rdesktop-brute

> rdesktop RDP client patched for password guessing (dictionary file or stdin-driven).

## What it's for

An rdesktop 1.5.0 build carrying the foofus "password guess" patch (originally by patrik@cqure.net, extended by jmk@foofus.net for use with the Medusa brute-forcer): a normal RDP client whose `-p` option doubles as the brute-force trigger. When `-p` names a readable file, that file is used as a password dictionary and each line is tried against the target until one succeeds; when `-p` is given as `-`, candidate passwords are read from stdin, which is how an external harness drives the guessing. Logon outcomes are classified from the RDP logon-error messages — expired password, interactive-logon denied by policy, session limits and "already logged on" all count as a valid password — and a `-l logfile` records the run. The rest is stock rdesktop 1.5.0 (`-u` user, `-d` domain, target host). It is a 2007-era legacy client meant for lab work against old Windows terminal servers; the patch itself warns that Windows 2000 targets only partially work.

## Typical use cases

### Dictionary attack from a password file

Try every candidate in a file against one account on a terminal server.

```
rdesktop-brute -u administrator -p passwords.txt -l rdp-guess.log 198.51.100.10
```

Outcome: connects to `198.51.100.10` repeatedly, one logon attempt per line of `passwords.txt`; guesses classified as valid (including policy-type failures that prove the password) are flagged, and `rdp-guess.log` records the attempt log for review.

### Stdin-driven guessing (external brute-forcer harness)

Feed candidates through stdin so another tool or loop controls the wordlist and pace.

```
printf 'Password1\nPassword2\n' | rdesktop-brute -u user -d CORP -p - -l rdp-guess.log 198.51.100.10
```

Outcome: announces "Starting brute-force attack via STDIN" on stderr, consumes one password per line from the pipe until EOF or a successful logon, and writes the logon status for each guess to `rdp-guess.log` where the driving process can read the verdict.

*No corpus evidence — derived from upstream documentation.*
