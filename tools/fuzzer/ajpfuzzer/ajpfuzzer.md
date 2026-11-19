---
name: "ajpfuzzer"
categories:
  - blackarch-fuzzer
version: "0.7"
homepage: "https://github.com/doyensec/ajpfuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "doyensec/ajpfuzzer"
github-stars: 98
github-pushed: "2022-11-15"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ajpfuzzer

> Interactive Java CLI that crafts, mutates and replays AJPv1.3 (ajp13) messages against Apache JServ Protocol connectors.

## What it's for

AJPFuzzer (Doyensec, built on their libajp13 library) speaks AJPv1.3 — the protocol J2EE containers such as Tomcat expose on AJP connectors and that web servers reach through modules like mod_jk or mod_proxy_ajp. It opens an interactive shell in which you connect to an AJP endpoint and send any of 22 built-in test cases: plain protocol messages (CPing, ForwardRequest, SendHeaders), format and length abuse (hugeheader, hugepacketsize), and named vulnerability probes (jettyleak in Ghostcat style, verbtampering, bypassauthnull). Messages can be fully hand-specified or fuzzed element-by-element with a wordlist. It is for security-testing AJP-based services; it does not fuzz other protocols and has no autonomous loop — you drive each message interactively.

## Typical use cases

### Connect to an AJP connector

Start the shell and establish a session to the target.

```
ajpfuzzer
connect 198.51.100.10 8009
```

Outcome: opens the AJPFuzzer shell — `?list` enumerates every message command and `?list <command>` shows its arguments — then connects to the ajp13 listener; the prompt changes to AJPFuzzer/198.51.100.10:8009.

### Probe protocol liveness with a CPing

Check that the endpoint actually speaks ajp13.

```
10
```

Outcome: sends a CPing (message type 10) and prints the raw AJP response; a CPong reply confirms a live AJP connector.

### Send a hand-crafted ForwardRequest

Exercise a request exactly as a reverse proxy would forward it.

```
forwardrequest 2 "HTTP/1.1" "/api/" 198.51.100.10 target.example.com target.example.com 8009 false "Cookie:AAAA=BBBB" ""
```

Outcome: builds and sends a fully specified ForwardRequest (method, protocol, URI, addresses, headers) and shows the container's answer on the same session.

### Fuzz a header value with a wordlist

Mutate one element of the request per wordlist entry.

```
genericfuzz 2 "HTTP/1.1" "/test.html" "198.51.100.10" "198.51.100.10" "target.example.com" 8009 false "Cookie:AAAA=BBBB" "secret:FUZZ" /path/to/list.txt
```

Outcome: sends one ForwardRequest per wordlist line with the `FUZZ` keyword replaced — compare responses across lines for anomalies, crashes or authentication bypasses.

### Fire a named vulnerability probe

Send a ready-made attack pattern.

```
verbtampering
```

Outcome: sends multiple AJP13 requests performing HTTP verb tampering to expose authentication-bypass flaws; sibling probes include jettyleak (Ghostcat-style file read), fuzzbit (bit-flipping a ForwardRequest) and hugeheader (oversized header lengths).

*No corpus evidence — derived from upstream documentation.*
