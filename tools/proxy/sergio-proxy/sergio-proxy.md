---
name: "sergio-proxy"
categories:
  - blackarch-proxy
version: "20.8a91bb4"
homepage: "https://github.com/supernothing/sergio-proxy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "supernothing/sergio-proxy"
github-stars: 52
github-pushed: "2015-03-15"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sergio-proxy

> Plugin-based HTTP MITM proxy built on sslstrip for intercepting and manipulating web traffic.

## What it's for

Sergio Proxy ("Super Effective Recorder of Gathered Inputs and Outputs") is a Python/Twisted HTTP man-in-the-middle proxy based on Moxie Marlinspike's sslstrip, written by Ben Schmidt. It sits where victim HTTP traffic is routed, strips SSL, logs traffic, and runs attack plugins — bundled examples cover capturing SMB challenge-response hashes and the Upsidedownternet image-flipping prank, plus a Metasploit integration option group and an ARP-spoof plugin hook. It is self-described alpha software: Python 2 era, HTTP/1.0 only, predating modern TLS, so treat it as a legacy tool for lab interception rather than a current attack platform.

## Typical use cases

### Discover options and bundled plugins

Enumerate everything this install of the proxy can do.

```
sergio-proxy -h
```

Outcome: argparse usage is printed with the sslstrip logging options (`-w`, `-p`, `-s`, `-a`, `-l`, `-f`, `-k`), the MSF integration group (`--msf-path`, `--msf-rc`, `--msf-user`, `--msf-lhost`), and one `--<name>` switch per detected plugin — that plugin list is the authoritative inventory of available attacks on this system.

### Run the intercepting proxy with full logging

Start the proxy and record everything passing through it.

```
sergio-proxy -l 10000 -a -w sergio.log
```

Outcome: the proxy listens on port 10000; route victims' port-80 traffic to it (e.g. with an iptables REDIRECT rule) and all HTTP and stripped-SSL traffic is written to sergio.log with timestamps. Swap `-a` for `-p` to log only SSL POSTs, add `-f` to substitute a lock favicon on secure requests, or `-k` to kill sessions in progress.

### Load a saved option set from a file

Keep long invocations in versioned argument files.

```
sergio-proxy @proxy.args
```

Outcome: options are read from proxy.args (one per line, `@` prefix) instead of the command line and mix freely with inline flags; the `.args` examples shipped in the repository's examples folder show the expected format.

*No corpus evidence — derived from upstream documentation.*
