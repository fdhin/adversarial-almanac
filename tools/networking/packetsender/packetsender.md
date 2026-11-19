---
name: "packetsender"
categories:
  - blackarch-networking
version: "1326.9714b2e"
homepage: "https://github.com/dannagle/PacketSender"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-08-24"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "dannagle/PacketSender"
github-stars: 2686
github-pushed: "2026-10-11"
github-archived: false
github-issues: 65
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 148
gh-activity-checked: "2026-11-09"
---

# packetsender

> GUI and CLI utility to send, receive, save and stress-test TCP, UDP, SSL and HTTP traffic.

## What it's for

Packet Sender (by Dan Nagle / NagleCode) is a multi-platform network test utility. Craft a packet once — destination, port, ASCII or hex payload — send it over TCP, UDP, SSL/TLS or HTTP, read the response in hex and ASCII, and save packets by name for reuse or sharing. The same engine drives a command line (`packetsender [options] address port data`) covering client sends, a server mode with canned macro responses, Wake-on-LAN, and an intense traffic generator with rate/bps limits. Use it to debug protocols, test listeners, or measure a service under load; it is a traffic testing tool, not a sniffer — it only sees replies to its own sends and traffic hitting its own servers.

## Typical use cases

### Send a TCP payload and capture the reply

One-shot protocol probe.

```
packetsender -taw 500 target.example.com 21 "USER user\r\nPASS Password1\r\n"
```

Outcome: opens a TCP connection to port 21, sends the ASCII payload (`-a`), waits up to 500 ms (`-w`), and prints the server's response as hex and ASCII — e.g. an FTP banner `220-Welcome...`.

### Run a UDP server with a canned response

Listen and auto-reply, e.g. to mock a service.

```
packetsender -l -u -b 8080 -r "{{TIME}}"
```

Outcome: binds a UDP server on port 8080; every arriving packet is printed with its source and timestamp, and the `{{TIME}}` macro is expanded into the reply sent back to the client.

### Send an HTTP request over TLS

Use the built-in HTTP modes.

```
packetsender --http GET "https://example.com/get"
packetsender --http POST "https://example.com/post" "{}"
```

Outcome: performs a GET (or a POST with the given body) over HTTPS and prints the response — the CLI equivalent of the GUI's HTTP test panel.

### Replay a saved packet by name

Resend a packet stored in the packet set.

```
packetsender -n "My Awesome Packet"
```

Outcome: the packet previously saved under that name is sent with its stored address, port and payload; any additional CLI options override the saved parameters.

### Send a Wake-on-LAN magic packet

Wake a machine remotely.

```
packetsender --wol aa:bb:cc:dd:ee:01
```

Outcome: the WOL magic packet for MAC `aa:bb:cc:dd:ee:01` is broadcast (optionally to a custom port), waking any host whose BIOS/UEFI is configured for Wake-on-LAN.

### Generate traffic at a fixed rate

Drive a load test from the CLI.

```
packetsender --rate 20 -n "My Awesome Packet"
```

Outcome: re-sends the saved packet 20 times per second until interrupted; `--bps`, `--num`, `--usdelay` and `--max` are the alternative rate controls for bandwidth-limited or flat-out testing.

*No corpus evidence — derived from upstream documentation.*
