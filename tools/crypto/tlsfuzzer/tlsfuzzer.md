---
name: "tlsfuzzer"
categories:
  - blackarch-crypto
version: "1961.5eebc44"
homepage: "https://github.com/tlsfuzzer/tlsfuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "tlsfuzzer/tlsfuzzer"
github-stars: 637
github-pushed: "2026-11-08"
github-archived: false
github-issues: 282
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 27
gh-activity-checked: "2026-11-09"
---

# tlsfuzzer

> SSL/TLS protocol test suite — a library plus 170+ ready-made scripts that check how a server handles malformed, edge-case, and attack-specific exchanges (ROBOT, Bleichenbacher oracles, RFC conformity).

## What it's for

tlsfuzzer tests SSLv2 through TLS 1.3 implementations by running scripted conversations against a server and verifying not merely that it survives, but that it reacts correctly — rejecting malformed input with the right alerts and negotiating only what the standards allow. Unlike a classic crash-fuzzer, every script in `scripts/` encodes expectations, so a run that prints no errors is a pass. It is a harness for servers you control (scripts connect out to a host and port, typically a local test instance started with openssl s_server or similar); it performs no certificate validation checks of its own and does not audit configuration hygiene the way testssl.sh or SSLyze do. Timing side-channel testing (Lucky13, Bleichenbacher timing) is supported through the project's TIMING workflow.

## Typical use cases

### Run a protocol-conformity check against a local test server

Verify a server under test rejects input the RFCs say it must refuse.

```
scripts/test-invalid-compression-methods.py -h 127.0.0.1 -p 4433
```

Outcome: executes the scripted conversation — here handshakes offering compression methods the server must not accept — against 127.0.0.1:4433; no output means every expectation was met, and each printed failure names the message and the violated expectation. Note that `-h` sets the hostname; use `--help` to list a script's options. Run scripts from a checkout of the repository.

### Test a server for a Bleichenbacher/ROBOT-class oracle

Check whether RSA key exchange leaks PKCS#1 padding validity.

```
scripts/test-bleichenbacher-workaround.py -h 127.0.0.1 -p 4433
```

Outcome: drives handshakes with crafted RSA ciphertexts designed to distinguish error paths, the ROBOT attack family; silent completion means the server's responses were indistinguishable as required, while reported errors indicate oracle behavior worth investigating before shipping the service.

### Fuzz a TLS 1.3 server with edge-case messages

Exercise modern-protocol corner cases the implementation may not expect.

```
scripts/test-tls13-0rtt-garbage.py -h 127.0.0.1 -p 4433
```

Outcome: negotiates TLS 1.3 and feeds garbage early-data (0-RTT) content, checking that the server handles it per specification; as with all tlsfuzzer scripts, absence of error output is the pass condition.

*No corpus evidence — derived from upstream documentation.*
