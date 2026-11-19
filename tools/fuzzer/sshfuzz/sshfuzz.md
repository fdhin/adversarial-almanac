---
name: "sshfuzz"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "https://packetstormsecurity.com/fuzzer/sshfuzz.txt"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sshfuzz

> Perl SSH/SCP fuzzer by Jeremy Brown that replays overflow, format-string and boundary-value strings over an authenticated session.

## What it's for

sshfuzz probes SSH servers and the SCP file-transfer channel riding on them for parsing flaws. It connects with Net::SSH2, authenticates using a username and password you supply, then pushes classic fuzz corpora — long "A" overflows up to 12,000,000 bytes, format strings such as `%n` and `%.2049d`, boundary integers like `2147483648`, and command-injection strings — through SCP get/put and other session operations. It is a simple sequential fuzzer with no built-in crash detection: results are triaged by hand from the `sshfuzz.log` file it appends to in the working directory, where the next-to-last logged fuzz string usually hints at the input that killed the connection. It requires valid credentials on the target and can leave very large log files behind.

## Typical use cases

### Fuzz an SSH server over its SCP operations

Run the fuzzer against a lab SSH daemon using a throwaway account.

```
sshfuzz -H 198.51.100.10 -P 22 -u user -p Password1
```

Outcome: repeatedly connects and authenticates, feeding each fuzz string through the SSH/SCP operations while printing stage progress ("Fuzzing [SCP]->[scp_get] STAGE #1 COMPLETE..."); every attempt is appended to `./sshfuzz.log`, and after a crash or hang the last logged entry identifies the offending input — raise `-P` for a daemon on a non-standard port.

*No corpus evidence — derived from upstream documentation.*
