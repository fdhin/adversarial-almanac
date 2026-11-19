---
name: "siparmyknife"
categories:
  - blackarch-voip
version: "11232011"
homepage: "http://packetstormsecurity.com/files/107301/sipArmyKnife_11232011.pl.txt"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "foreni-packages/siparmyknife"
github-stars: 2
github-pushed: "2015-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# siparmyknife

> Blake Cornell's Perl SIP fuzzer — mutates SIP requests with string overflows, format strings, XSS/SQLi payloads and hash substitutions to crash or hang SIP servers and phones.

## What it's for

Fuzzing the request-handling path of SIP devices. SIP Army Knife (a Perl script by Blake Cornell, distributed via Packet Storm as `sipArmyKnife_11232011.pl.txt`) generates a matrix of mutated SIP requests — BYE, OPTIONS, PRACK, PUBLISH, INFO, MESSAGE, REFER, SUBSCRIBE, NOTIFY, and REGISTER/INVITE when the target looks like a PBX — over TCP or UDP. Mutations include long repeated strings (128 to 32768 bytes of 'A', NUL bytes, CR/LF, ESC), classic format strings (`%n`, `%s`, `%p`, ...), integer and string format confusions, injected headers, XSS and SQL injection strings, and MD5/MD4/CRC32/CRC16 substitutions aimed at digest handling (the source itself warns these "can cause timeouts"). It is a single-request mutation fuzzer, not a stateful call-flow tool: it will not negotiate registrations or media. For structured SIP auditing see [[sippts]]; for protocol torture traffic see [[sipsak]]'s randtrash mode.

## Typical use cases

### Fuzz a SIP server with overflow and format-string mutations

```
siparmyknife -h 198.51.100.10 -p 5060 --proto UDP --stringOverflows --stringFormats -d 50
```

Outcome: sends the mutated request matrix to the target over UDP with mutation density limited to 50; watch the target with tcpdump/Wireshark and keep a second probe (e.g. `siparmyknife --getOptions`) running — a target that stops answering or crashes mid-run has a parser or format-string finding. Add `-v`/`-vv` to print each request as it is sent.

### Detect what the target is, then fuzz accordingly

```
siparmyknife -h 198.51.100.10 -p 5060 --proto UDP --detectVersion -v
```

Outcome: sends an initial OPTIONS probe and prints `Server Header Detected: ...` (a PBX — REGISTER and INVITE are added to the fuzzed methods) or `Phone Version Detected: ...` (an end user device — the method list is trimmed), then continues the fuzz run against the adjusted request set.

### Send a single OPTIONS probe

```
siparmyknife -h 198.51.100.10 -p 5060 --proto UDP --getOptions
```

Outcome: sends one OPTIONS request and prints the raw response — a quick liveness and banner check before committing to a longer fuzz run.

### Fuzz with hash substitutions

```
siparmyknife -h 198.51.100.10 -p 5060 --proto UDP --md5 --crc32
```

Outcome: request mutations include replaced MD5 and CRC32 digest material aimed at the target's authentication parsing; these are the mutation classes the author flags as most likely to wedge a device, so monitor for hangs as well as crashes.

*No corpus evidence — derived from upstream documentation.*
