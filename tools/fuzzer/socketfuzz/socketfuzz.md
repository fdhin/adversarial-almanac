---
name: "socketfuzz"
categories:
  - blackarch-fuzzer
version: "26.089add2"
homepage: "https://github.com/landw1re/socketfuzz"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "landw1re/socketfuzz"
github-stars: 8
github-pushed: "2017-03-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# socketfuzz

> Single-target TCP fuzzer that walks a service from growing buffers to a working exploit (crash → offset → bad characters → shellcode).

*No corpus evidence — derived from upstream documentation.*

## What it's for

socketfuzz is a Python buffer-overflow development fuzzer aimed at one TCP service: instead of generating protocol-aware traffic, it drives the classic manual exploit workflow against a chosen port — send growing buffers until the service dies, send a cyclic pattern and locate the EIP offset, test which characters survive transit (bad characters), find space and a return address for shellcode, and finally replay the exploit buffer with shellcode attached. The fuzzable buffers are built in: the current release fuzzes the POP3 PASS field (`-f 1`). The `--rand` mode depends on Metasploit's `pattern_create` utility being available. It is a crash-driven, single-target tool meant to be used alongside a debugger, not a broad protocol fuzzer.

## Typical use cases

### Find the crash size with a growing buffer

Sends increasingly long buffers until the service stops responding.

```
socketfuzz -i 198.51.100.10 -p 110 -f 1 --growing-buffer
```

Outcome: transmits POP3 password buffers that grow in `-n` increments (default 200) up to `-s` bytes (default 2000) filled with `-c` (default `A`); the size reported when the service stops responding is the crash size to carry into the next steps.

### Locate the EIP offset with a cyclic pattern

Turns a crash into a precise overwrite offset.

```
socketfuzz -i 198.51.100.10 -p 110 -f 1 --rand -s 3000
socketfuzz -i 198.51.100.10 -p 110 -f 1 --find-offset -s 3000 -e "\x39\x69\x44\x30"
```

Outcome: the first command sends a 3000-byte Metasploit cyclic pattern; after the crash, read EIP in the debugger and pass its bytes to `--find-offset`, which prints the offset into the pattern at which EIP was overwritten.

### Check bad characters, then deliver shellcode

Completes the exploit once offset and return address are known.

```
socketfuzz -i 198.51.100.10 -p 110 -f 1 --check-badchars -l 1032 -r "\x00,\x0a"
socketfuzz -i 198.51.100.10 -p 110 -f 1 --send-exploit -l 1032 -a "\x8f\x35\x4a\x5f" -x shell.bin
```

Outcome: the first run sends all bytes 0x00–0xff (minus the removed ones) at the buffer location so a debugger memory dump reveals which characters get mangled; the second places the return address at offset 1032 and appends the shellcode from `shell.bin` for the final controlled test.
