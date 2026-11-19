---
name: "driftnet"
categories:
  - sniffing-spoofing
version: "1.5.0-0.3"
homepage: "https://github.com/deiv/driftnet"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, nixpkgs]
build-system: make
meta-checked: "2026-11-09"
github-repo: "deiv/driftnet"
github-stars: 471
github-pushed: "2025-05-01"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# driftnet

> Plucks images (and MPEG audio) out of passing network traffic and displays or saves them.

## What it's for

driftnet (originally by Chris Lightfoot, maintained by David Suárez) watches a network interface and reassembles image files — JPEG, GIF, PNG and WebP — from live TCP streams, popping them up in a window as they flow past: a graphical tcpdump for other people's browsing. It can additionally capture streamed MPEG audio and play it through an external player, dump captured HTTP request text, and instead of opening a window it can run in adjunct mode, saving each extracted file to a directory and printing its name for consumption by other programs, or serve the images over a small built-in HTTP server. It only sees cleartext traffic (it does not decrypt TLS), needs raw-packet privileges, and it demonstrates exactly why unencrypted HTTP is a privacy problem.

## Typical use cases

### Display images from live traffic

Watch what a segment's users are viewing, in real time.

```
sudo driftnet -i eth0
```

Outcome: a GTK window opens and fills with decoded images as they cross the interface; clicking an image saves it. On an unencrypted HTTP segment this shows every picture anyone is loading, seconds after they load it.

### Save images without a display

Collect extracted files for later review, headless.

```
sudo driftnet -i eth0 -a -d /tmp/driftnet -x session-
```

Outcome: adjunct mode saves each captured image into `/tmp/driftnet` with the prefix `session-` and prints each filename to stdout as it lands, so a script can tail the output and process files (drop silently beyond a cap with `-m`). No window is opened.

### Serve captures as a web page

Distribute the live view to a browser instead of a local window.

```
sudo driftnet -i eth0 -w -W 9090
```

Outcome: driftnet starts its built-in HTTP server on port 9090 (the `-W` flag implies `-w`); pointing a browser at `http://localhost:9090` shows images as they are captured — a display option for headless hosts with a desktop elsewhere.

### Extract images from a saved capture

Process a pcap taken earlier, or piped from a decoder.

```
driftnet -f capture.pcap -a -d ./extracted
```

Outcome: driftnet reads packets from the pcap dump file instead of an interface (the filename may be a named pipe, e.g. fed by a wireless sniffer's decrypted output) and writes every extractable image into `./extracted`.

### Capture streamed MPEG audio

Grab what the network is listening to.

```
sudo driftnet -i eth0 -s -M "mpg123 -"
```

Outcome: streamed MPEG audio is extracted and piped to the named player command (default `mpg123 -`) and played as it arrives; use `-S` to capture audio only and ignore images.

### Dump captured HTTP request text

Turn the capture into request lines instead of media.

```
sudo driftnet -i eth0 -t
```

Outcome: HTTP request text seen on the wire is written to stdout — a quick way to see which URLs clients request on a cleartext segment; `-T` restricts the capture to text and ignores images and audio.

*No corpus evidence — derived from upstream documentation.*
