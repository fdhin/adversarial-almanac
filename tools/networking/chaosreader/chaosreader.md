---
name: "chaosreader"
categories:
  - blackarch-networking
version: "0.94"
homepage: "http://chaosreader.sourceforge.net/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "brendangregg/Chaosreader"
github-stars: 240
github-pushed: "2021-08-21"
github-archived: false
github-issues: 10
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chaosreader

> Turn tcpdump/snoop captures into per-session HTML reports, extracted files and replayable interactive sessions.

## What it's for

A Perl "any-snarf" tool by Brendan Gregg that reads tcpdump/libpcap or snoop capture files, reassembles TCP, UDP, ICMP and IP fragments, splits the traffic into sessions, and writes an HTML index linking to everything it recovered: colour-coded both-way transcripts of telnet/IRC/rlogin sessions, HTTP pages and images, FTP transfers, SMTP email, plus raw data files and replay scripts that play an interactive session back in real time. It was written to demonstrate how much an unencrypted packet dump leaks; it decrypts nothing. It can also run standalone, invoking tcpdump or snoop itself to produce the capture.

## Typical use cases

### Extract all sessions from a capture file

Post-process a packet dump into browsable reports.

```
chaosreader capture.pcap
```

Outcome: writes `index.html` and `index.text` plus per-session files (`session_0001.telnet.html`, `session_0002.part_01.jpeg`, ...) in the current directory — open `index.html` to browse every session and recovered file. Run it in a clean directory; it creates many files.

### Focus on specific ports

Only reassemble the services you care about.

```
chaosreader -p 20,21,23 capture.pcap
```

Outcome: builds session files only for TCP/UDP ports 20, 21 and 23 (FTP and telnet), skipping the rest of the capture.

### Capture and process in one step

Let the tool drive the sniffer.

```
chaosreader -s 5
```

Outcome: invokes tcpdump (or snoop, on Solaris) itself, captures traffic for 5 minutes, then processes the log as usual — no separate capture command needed; `-S 2,5` repeats this as multiple 2-minute samples.

### Dump everything, not just recognised protocols

Force full session output.

```
chaosreader -e capture.pcap
```

Outcome: creates HTML and hex two-way dumps for every session, including traffic on ports the tool does not recognise by service name.

*No corpus evidence — derived from upstream documentation.*
