---
name: "ftp-fuzz"
categories:
  - blackarch-fuzzer
version: "1337"
homepage: "http://nullsecurity.net/tools/fuzzer.html"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-23"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# ftp-fuzz

> Nullsecurity's single-file Python FTP fuzzer: optionally logs in, then walks every FTP command with character-filled buffers of growing length.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ftp-fuzz.py (by TheXero, nullsecurity team, 2012) is a straightforward dumb fuzzer aimed at FTP server software. For each filler character (printable ASCII; with `-e` also bytes 0x01–0xff) and each of roughly 50 FTP commands — the RFC 959 core plus extensions such as AUTH, EPRT, FEAT, LANG, MDTM, MIC, MLSD, OPTS, SITE, XCUP — it opens a fresh connection, optionally authenticates, and sends `COMMAND <char repeated N times>` with N growing from 100 up to the maximum buffer size. The interesting signal is the command/character/length at which the server stops accepting connections; the tool ships no instrumentation, so the daemon is watched from the outside (logs, coredumps) while it runs.

## Typical use cases

### Fuzz an anonymous FTP server

Sweep every command on the standard port.

```
ftp-fuzz -t 198.51.100.10
```

Outcome: the tool connects repeatedly to port 21 and prints `Fuzzing <COMMAND> with <N> bytes of <char>` for each attempt, with buffers up to the default 10000 bytes; when the output stalls or connections start timing out, note the command and buffer shown, restart the daemon, and focus there.

### Fuzz with authentication on a custom port

Reach commands an anonymous session is denied.

```
ftp-fuzz -t 198.51.100.10 -p 2121 -U user -P Password1
```

Outcome: each cycle logs in with the given credentials before sending the fuzzed command, exercising session-gated commands like STOR and RETR; specifying `-U` without `-P` prints help and exits.

### Extend the character set and buffer length

Push naive parsers with non-ASCII filler and longer buffers.

```
ftp-fuzz -t 198.51.100.10 -m 50000 -e
```

Outcome: the maximum buffer grows to 50000 bytes (`-m`) and bytes 0x01–0xff join the filler set (`-e`), the mode most likely to upset command parsers that assume printable input.
