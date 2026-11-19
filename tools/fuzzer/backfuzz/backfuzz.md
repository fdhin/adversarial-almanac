---
name: "backfuzz"
categories:
  - blackarch-fuzzer
version: "1.b0648de"
homepage: "https://github.com/localh0t/backfuzz"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "localh0t/backfuzz"
github-stars: 131
github-pushed: "2019-06-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# backfuzz

> Python protocol-fuzzing toolkit that fires generated patterns (cyclic, single-char, format-string) at TCP/UDP services through per-protocol plugins.

## What it's for

Back To The FUZZ'er (backfuzz, by localh0t) is a brute-force network protocol fuzzer: pick one of its plugins — ftp, http, imap, irc, pop, smtp, ssh, tcp, tftp, tnet (telnet), udp — a length range and a pattern flavour, and it sends progressively longer fuzz strings framed for that protocol. The cyclic pattern flavour is the workhorse: when the service crashes, the pattern bytes recovered from the crash dump reveal the exact offset that was overwritten. There is no coverage feedback and no protocol state modeling — this is the dumb-fuzzer end of the spectrum, useful for quick socket-level abuse tests. It is written for Python 2 (current systems need a python2 interpreter alongside), and its `-h` flag sets the target host rather than help; running it with no arguments prints the usage screen.

## Typical use cases

### List plugins and usage

See which protocols and pattern flavours are available.

```
python2 backfuzz.py
```

Outcome: prints the argument summary, the four pattern flavours (Cyclic, Cyclic Extended, Single, FormatString) and every available protocol plugin, then exits without sending anything.

### Fuzz an FTP service with cyclic patterns

Locate crash offsets on a classic text protocol.

```
python2 backfuzz.py -h 198.51.100.10 -p 21 -min 10 -max 500 -s 50 -pl ftp
```

Outcome: sends ftp-framed fuzz strings growing from 10 to 500 bytes; a resulting crash can be mapped to an exact buffer offset by locating the cyclic pattern in the fault dump.

### Fuzz with format strings

Target printf-style string handling.

```
python2 backfuzz.py -h 198.51.100.10 -p 80 -min 10 -max 400 -s 20 -pl http -pf FormatString
```

Outcome: fuzz strings become `%n%x%s` sequences — a service that passes them into format functions crashes or leaks stack addresses in its replies.

### Show the pattern that killed the target

Capture the failing input directly and tolerate slow targets.

```
python2 backfuzz.py -h 198.51.100.10 -p 22 -min 5 -max 200 -s 10 -pl ssh -t 2 -S
```

Outcome: on a crash the exact fuzz string that broke the session is printed (`-S`), while `-t 2` raises the per-attempt timeout from the 0.8 s default to 2 seconds.

*No corpus evidence — derived from upstream documentation.*
