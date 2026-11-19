---
name: "dirstalk"
categories:
  - blackarch-scanner
version: "1.3.3"
homepage: "https://github.com/stefanoj3/dirstalk"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-07-13"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "stefanoj3/dirstalk"
github-stars: 397
github-pushed: "2023-12-24"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dirstalk

> Modern multithreaded Go brute-forcer for paths on web servers, in the dirb/dirbuster lineage.

## What it's for

dirstalk brute-forces paths on web servers with configurable recursion
depth, HTTP methods, threading, cookies and headers — functionally a modern
replacement for dirb/dirbuster as a single static binary. Its dictionary
argument accepts a local file or a remote URL, and it ships its own
dictionary generator that turns a local directory tree into a wordlist.
Every option lives under subcommands (`dirstalk <command> -h` is
self-documenting); the `scan` command does the work.

## Typical use cases

### Basic dictionary scan

Fuzz a web root with a wordlist.

```
dirstalk scan http://target.example.com/ --dictionary /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

Outcome: multithreaded scan (default 3 threads, depth 3) of the target
using the given dictionary; each response that is not a 404 is reported
with status and path — the DirBuster lists are a natural pairing, and
`--dictionary` also accepts an `http://` URL to fetch the list remotely.

### Tuned scan: deeper recursion, more threads, POST too

Adapt the scan to a large target.

```
dirstalk scan http://target.example.com/ --dictionary words.txt --scan-depth 10 --threads 20 --http-methods GET,POST --http-timeout 10000
```

Outcome: recurses up to 10 levels deep with 20 concurrent workers, sends
both GET and POST per candidate, and uses a 10-second HTTP timeout;
`--http-statuses-to-ignore 404,301` trims what counts as a result.

### Scan through Tor or a SOCKS5 proxy

Hide or route the scan traffic.

```
dirstalk scan http://target.example.com/ --dictionary words.txt --socks5 127.0.0.1:9150 --user-agent Mozilla/5.0
```

Outcome: every request goes through the local SOCKS5 proxy (e.g. a Tor
container publishing 127.0.0.1:9150) with the chosen User-Agent; cookies
and extra headers can be added with `--cookie name=value` and
`--header "Authorization: Bearer ..."`.

### Generate a dictionary from a local directory tree

Turn known file layouts into a targeted wordlist.

```
dirstalk dictionary.generate /var/www/html --out mydictionary.txt
```

Outcome: walks the given local path and writes every relative file/dir
path to `mydictionary.txt` (or stdout when `--out` is omitted), producing a
dictionary tailored to applications shaped like the local copy.

*No corpus evidence — derived from upstream documentation (stefanoj3/dirstalk
README, the packaged upstream).*
