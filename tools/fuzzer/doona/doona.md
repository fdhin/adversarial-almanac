---
name: "doona"
categories:
  - blackarch-fuzzer
version: "145.7a4796c"
homepage: "https://github.com/wireghoul/doona"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "wireghoul/doona"
github-stars: 77
github-pushed: "2022-02-17"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# doona

> Fork of the Bruteforce Exploit Detector (BED) that replays known-bad payloads into network protocol daemons to find buffer overflows, format strings and other input-handling faults.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Doona (by Eldar "Wireghoul" Marcussen) stress-tests network daemons by feeding oversized, malformed and format-string payloads into protocol commands and watching whether the service survives. It keeps BED's per-protocol modules — ftp, http (plus http_more, http_sp, http_webdav), imap, irc, lpd, nntp, pjl, pop, proxy, rtsp, smtp, socks4, socks5, tftp, finger, whois, dict — and adds a larger fuzz-payload set and new operational switches: periodic health checks with retry, resuming a run at a specific test-case index, and capping the number of cases. It is a protocol daemon fuzzer, not a web-application fuzzer: targets are raw TCP services.

## Typical use cases

### Fuzz a daemon on its port

Sweep a lab FTP service with the ftp module's payload set.

```
doona -m ftp -t 198.51.100.10 -p 21
```

Outcome: doona iterates the ftp module's fuzz cases against the service, printing its banner and each test as it runs; a repeated failure to connect usually means the daemon crashed — restart it and note the last test case shown for a resume.

### Keep an unattended run alive across crashes

Health-check the service periodically and retry until it recovers.

```
doona -m smtp -t 198.51.100.10 -p 25 -c 50 -k
```

Outcome: after every 50 fuzz cases (`-c`) doona checks that the service still responds, and with `-k` it keeps retrying the check until the service is back instead of aborting, so long runs ride out crashes without supervision.

### Resume at a crashing test case and capture it

Re-run from the index where a crash was observed and dump the exact payload.

```
doona -m http -t 198.51.100.10 -p 80 -r 1337 -d
```

Outcome: resumes the fuzz run at test case 1337 (`-r`), and `-d` prints the fuzz case to stdout so it can be replayed manually (for example through netcat) against a restarted daemon to confirm the crash.

### Bound a run for slow targets

Cap the case count and give the service time to recover between cases.

```
doona -m pop -t 198.51.100.10 -p 110 -M 100 -o 5
```

Outcome: stops after 100 fuzz cases (`-M`) and waits 5 seconds after each test (`-o`, default 2), which keeps slow or embedded services from collapsing mid-run before useful results are collected.
