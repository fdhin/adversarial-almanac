---
name: "ftp-scanner"
categories:
  - blackarch-cracker
version: "0.2.5"
homepage: "http://wayreth.eu.org/old_page/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-04"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ftp-scanner

> Multi-threaded FTP scanner and brute-forcer (by Inode) that sprays user and password lists across a host range and logs working credentials.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Walks an IP or CIDR range, connects to FTP on every host, and tries every password in a list against every user in a list, recording successful logins to an output file along with the grabbed banner. Practical extras: stop after the first working password per user, save odd server replies, optionally probe Telnet/SSH on hosts where the FTP login worked, and test whether the login can delete a directory (write access). It is a 2008-era single-purpose C program from the author's now-defunct site — plain FTP only, no TLS, so it is useful against legacy embedded boxes and lab FTP services rather than modern servers. Mind the flag semantics: `-h` takes the target hosts (not help), and `-c` is the thread count (not a cookie flag).

## Typical use cases

### Brute-force FTP across a subnet

```
ftp-scanner -h 198.51.100.0/24 -u users.txt -p passwords.txt -c 20 -o results.txt
```

Outcome: threads fan out over the range (default 20, 5-second connect timeout) and print `Testing USER: ... PASS: ... IP: ...` progress; each valid login is appended to `results.txt` as an `IP:/USER:/PASS:` record. Stop the run and read the file for hits.

### Capture banners and stop after the first hit per user

```
ftp-scanner -h 198.51.100.10 -u users.txt -p passwords.txt -b -d -o results.txt
```

Outcome: with `-b`, the FTP banner line follows each result entry in the output file (useful for fingerprinting vs. version-specific exploits); with `-d`, brute-forcing of a user stops as soon as one password works, cutting noise and lockout risk.

### Check cracked hosts for Telnet/SSH exposure

```
ftp-scanner -h 198.51.100.0/24 -u users.txt -p passwords.txt -k -o results.txt
```

Outcome: for every host with a valid FTP login, the result line gains a ` Telnet` and/or ` SSH` suffix when those ports also answer — a quick way to spot where reused credentials could give interactive access.
