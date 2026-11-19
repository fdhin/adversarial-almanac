---
name: "rbkb"
categories:
  - blackarch-misc
version: "v0.7.3.r3.g4645025"
homepage: "https://github.com/emonti/rbkb"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-28"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "emonti/rbkb"
github-stars: 148
github-pushed: "2025-06-17"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rbkb

> A Ruby BlackBag of small command-line utilities for data conversion and network protocol debugging during pen-testing and reverse engineering.

## What it's for

rbkb ("Ruby BlackBag") is a collection of small command-line tools plus a monkey-patching Ruby library, written as a Ruby port of Matasano's C-language BlackBag (`bkb`) toolkit. One family of tools handles repetitive data chores — base64/hex/URL conversion, CRC32, repeating-key XOR, binary grep, reversing hexdumps back to raw data — and all of them read from arguments, files or stdin, so they compose cleanly in pipelines. A second family (`telson`, `plugsrv`, `blit`, `feed`) is an interactive protocol workbench: stub clients and reverse proxies whose live conversations can be hexdumped and injected into out-of-band while they run. It contains no exploits; it exists to remove typing.

## Typical use cases

### Base64-encode a file in place

Encode arbitrary payloads without reaching for a full scripting language.

```
b64 -f payload.bin
```

Outcome: writes the base64 of `payload.bin` to stdout (default one line; use `-l` to wrap at a fixed width). The inverse tool `d64` decodes the same way.

### Convert a delimited hex string back to raw bytes

Turn hex-dump snippets into binary data for further processing.

```
unhexify -d ':' 'de:ad:be:ef' > chunk.bin
```

Outcome: strips the delimiter, decodes each hex chunk and writes the 4 raw bytes to `chunk.bin`; `hexify` performs the reverse conversion with configurable prefix/suffix per byte.

### XOR data against a repeating hex key

Apply or strip a repeating-key XOR layer.

```
xor -x 'deadbeef' -f cipher.bin > plain.bin
```

Outcome: XORs every byte of `cipher.bin` against the repeating key `deadbeef` and writes the raw result to stdout; `-s` supplies an ASCII key instead.

### Search a binary for a hex pattern

Locate known byte signatures inside files or memory dumps.

```
bgrep -x 'deadbeef' firmware.bin
```

Outcome: prints each match with its offset information; without `-x` the subject is treated as literal text, and `-r` switches it to a regex.

### Reverse a hexdump back into binary

Rebuild the original file from `xxd` or `hexdump -C` output.

```
dedump dump.txt > original.bin
```

Outcome: parses the textual hexdump on stdin or from a file and emits the reconstructed raw bytes; `-l` adjusts bytes-per-line if the dump used a non-default width.

### Probe a service as an injectable stub client

Connect to an unknown protocol and interact with it message by message.

```
telson 198.51.100.10:8080
```

Outcome: opens a TCP connection that hexdumps everything the server sends and listens for `blit` messages (`-b`), letting you push raw data into the conversation mid-flight; `-u` switches to UDP and `-S` initiates TLS.

### Sit between a client and its server as an inspectable proxy

Watch and manipulate one side of a conversation at a time.

```
plugsrv 198.51.100.10:443@127.0.0.1:8443
```

Outcome: starts a reverse TCP proxy — point the client at `127.0.0.1:8443`; both directions of traffic are hexdumped, and blit messages can be injected toward either the client or the server end.

### Replay captured messages as a client

Bootstrap testing of an unfamiliar protocol from existing traffic.

```
feed --from-pcap session.pcap 198.51.100.10:443
```

Outcome: connects to the target and sends each captured message in sequence, printing replies; message sources can also be yaml, hexdump files or raw files, and `-l` flips feed into server mode instead.

*No corpus evidence — derived from upstream documentation.*
