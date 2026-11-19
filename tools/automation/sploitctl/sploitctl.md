---
name: "sploitctl"
categories:
  - blackarch-automation
version: "3.0.4"
homepage: "https://github.com/BlackArch/sploitctl"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "BlackArch/sploitctl"
github-stars: 118
github-pushed: "2023-10-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sploitctl

> Fetch, update and search local exploit archives from sites like exploit-db and packetstorm.

## What it's for

sploitctl (BlackArch) keeps a local exploit corpus: it downloads whole
exploit archives (exploit-db, packetstorm and similar sites) into a base
directory (default `/usr/share/exploits`), updates the archives it
installed, and greps them with a regex when you need an exploit for a
specific technology. It manages collections, not single files — for a
one-off lookup you still read the matched sources yourself. Archives are
fetched in parallel with retries, and downloads can be routed through a
proxy.

## Typical use cases

### List the available exploit archives

```
sploitctl -f ?
```

Outcome: prints the numbered menu of archive sources; those numbers are
what you pass to `-f` to download a specific archive (`0` means all).

### Download and unpack every exploit archive

```
sploitctl -f 0 -XR
```

Outcome: downloads all archives into `/usr/share/exploits`, decompresses
each (`-X`) and removes the compressed tarball afterwards (`-R`), leaving
a browsable local exploit tree.

### Download through Tor or a proxy

```
sploitctl -f 0 -P "socks5://127.0.0.1:9050" -A "noleak"
```

Outcome: all downloads go through the given SOCKS5 proxy with a custom
user-agent (`-A`), keeping the fetching host's identity out of the source
site's logs.

### Update the installed archives

```
sploitctl -u ?
sploitctl -u 0 -XR
```

Outcome: `-u ?` lists which archives are currently installed locally
(update IDs are relative to what is installed, so always list first);
`-u 0 -XR` then refreshes all of them, decompressing and cleaning up as
with downloads.

### Search the local exploit corpus

```
sploitctl -s vsftpd
```

Outcome: greps the base directory for the regex and prints every matching
exploit file path; any POSIX extended regex works, e.g. `-s 'apache.*2\.4'`
(with quoting).

*No corpus evidence — derived from upstream documentation.*
