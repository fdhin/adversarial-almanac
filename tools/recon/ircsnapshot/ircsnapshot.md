---
name: "ircsnapshot"
categories:
  - blackarch-recon
version: "94.cb02a85"
homepage: "https://github.com/bwall/ircsnapshot"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "bwall/ircsnapshot"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# ircsnapshot

> Connects to an IRC server as a benign client and dumps everything a new user can see — servers linked to the network, channels, users and whois data — into logs and JSON.

## What it's for

Profiling an IRC network from the inside during botnet/threat research: which servers a network is composed of (links), which channels exist and who sits in them, and per-user whois data, all gathered exactly the way an arriving bot would see it. Written by Brian Wallace (@botnet_hunter); it is a measurement tool, not an IRC client — one snapshot run produces structured output you graph or diff later. It does not interact with users, join passworded channels beyond the connecting user's privileges, or attack anything. The upstream repository is deleted; the packaged (Python 2) build is preserved in BlackArch, and the README survives via the Internet Archive. Proxy connections are SOCKS5-only and work with Tor; DNS queries are sent through the proxy.

## Typical use cases

### Snapshot a server's visible network state

```
python2 ircsnapshot.py -n reconbot -x irc.example.net 6697
```

Outcome: connects as nick `reconbot` over SSL (`-x`; drop it for plaintext, use `-p` for a server password, `-u`/`-r` to set the bot's user and real name), then enumerates links, channels and users. Writes two files named after the server: `irc.example.net.log.txt` (human-readable log) and `irc.example.net.json` — a structured document with `links`/`linkList`, `channels`, `userList` and `users` (whois data) keys ready for scripting.

### Convert a snapshot into a Gephi graph

```
python2 to.gexf.py UserToChannel irc.example.net.json > users-channels.gexf
```

Outcome: the bundled support script reads the JSON snapshot and emits GEXF on stdout — here a bipartite user-to-channel graph; `UserToLink` is the alternative conversion relating users to the servers they are connected through. Import the file into Gephi to visualize the network's social/infrastructure structure.

*No corpus evidence — derived from the upstream README as preserved by the Internet Archive (the GitHub repository is deleted).*
