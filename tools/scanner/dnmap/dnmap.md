---
name: "dnmap"
categories:
  - blackarch-scanner
version: "0.6"
homepage: "http://sourceforge.net/projects/dnmap/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "cldrn/dnmap"
github-stars: 21
github-pushed: "2022-07-14"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnmap

> Distributed nmap: a TLS client/server framework that farms nmap commands out to many machines.

## What it's for

dnmap (by eldraco, original project at dnmap.sourceforge.net) distributes
nmap scans across several clients: the server reads a text file of nmap
commands and hands them one by one to connected clients over TLS (default
port 46001); each client runs its command with the local nmap binary and
both sides keep the output. Use it when a large scan should be split over
several machines or internet connections. It is a trust-based tool between
friends — clients execute whatever nmap command the server sends, so only
point it at a server you control. Commands should be chunky (one subnet per
command, not one port) or the distribution overhead outweighs the gain.

## Typical use cases

### Start the server with a command file

Hand out nmap commands to connected clients.

```
printf 'nmap -sS -p 1-1000 198.51.100.0/24 -v -n -oA subnet-a\n' > commands.txt
dnmap_server -f commands.txt
```

Outcome: listens on TLS port 46001, hands each connected client the next
nmap command from `commands.txt`, stores per-client statistics (commands
run, uptime, commands/minute, status), and logs everything to
`dnmap.log`-style server logs; new lines appended to `commands.txt` are
picked up without a restart.

### Connect a client to the server

Add a scanning node.

```
dnmap_client -s 198.51.100.10 -a node1
```

Outcome: connects to the dnmap server at 198.51.100.10 (alias `node1`,
shown in the server's stats table), executes each received nmap command
locally, and — because it injects `-oA` when the command lacks one — always
keeps a local copy of every scan's output. If the server goes down the
client keeps retrying until it returns.

### Rate-limit a client

Keep a low profile regardless of what the server asks for.

```
dnmap_client -s 198.51.100.10 -a node1 -m 200
```

Outcome: the client caps its nmap at `--max-rate 200` packets per second,
stripping any rate the server's commands try to impose (`-m`, with `-d` for
debug output); non-root clients should receive TCP connect scans
(`-sT`-style commands), which the server operator controls by writing the
command file accordingly.

*No corpus evidence — derived from upstream documentation (dnmap 0.6 README
and the packaged dnmap_server/dnmap_client sources).*
