---
name: "tcpwatch"
categories:
  - blackarch-networking
version: "1.3.1"
homepage: "http://hathawaymix.org/Software/TCPWatch"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: python
license: ZPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tcpwatch

> Shane Hathaway's Python monitor for forwarded TCP and HTTP proxy connections — shows both directions of each session in a Tk window (or stdout) and can record every request/response pair to files.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Watching what a client and server actually say to each other while developing or debugging protocol implementations and web services. It runs either as a TCP forwarder (`-L listen:dest`) or as an HTTP proxy (`-p`), and by default displays each session in a Tkinter window with a history of past connections; `-s` prints to stdout instead when no X display is available. The recording option writes each request, response and error to separate files, which makes it a simple transcript tool for HTTP work. It is a passive observer/forwarder — it does not modify traffic, and it is not a general sniffer (it only sees connections that are routed through it).

## Typical use cases

### Watch a forwarded TCP connection live

```
tcpwatch -L 8080:target.example.com:80
```

Outcome: listens on local port 8080 and forwards to `target.example.com:80`; the Tk window shows each session with both directions of the conversation as it happens. Point the client at localhost:8080 and watch the protocol exchange.

### Dump the conversation to a terminal (no GUI)

```
tcpwatch -s -L 8080:target.example.com:80
```

Outcome: the same forwarded connection, but the traffic is written to stdout instead of a Tk window — usable over SSH or in a headless session.

### Run an intercepting HTTP proxy and split requests

```
tcpwatch -p 8080 -h
```

Outcome: listens as an HTTP proxy on 8080; configure a browser or tool to use it. With `-h` (HTTP mode), persistent HTTP/1.1 connections are split into separate per-request entries instead of one long stream.

### Record every request and response to files

```
tcpwatch -L 8080:target.example.com:80 -r /tmp/sessions --record-prefix target
```

Outcome: for each request, a `target-*.request` file with the client's bytes and a `target-*.response` file with the server's reply are written under `/tmp/sessions` (plus `.error` files for anomalies) — a file-based transcript suitable for diffing or replaying later.
