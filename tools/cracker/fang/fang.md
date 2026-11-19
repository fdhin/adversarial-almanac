---
name: "fang"
categories:
  - blackarch-cracker
version: "22.4f94552"
homepage: "https://github.com/evilsocket/fang"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "evilsocket/fang"
github-stars: 68
github-pushed: "2016-07-15"
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

# fang

> Multi-threaded MD5 cracker that fans a hash out to several online reverse-lookup services at once.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Farms hash "cracking" out to free online MD5 databases: service definitions (HTTP method, URL template with a `{HASH}` placeholder, regex to lift the answer off the result page) live in `fang.conf`, and one thread is spawned per service-and-hash pair, so every site is queried in parallel and the first positive answer is printed. It does no computation of its own — it can only recover plaintexts someone else has already cracked and published, so it is a cheap first pass before a real cracker, not a substitute. The bundled service list is 2011-era and most entries are dead by now, but the conf format makes it trivially editable. The script opens `fang.conf` from the current working directory (distributions install it at `/etc/fang.conf`), so run it from a directory containing a copy.

## Typical use cases

### Look up a single hash everywhere at once

```
fang --hash 7815696ecbf1c96e6894b779456d330e
```

Outcome: prints `Searching for '<hash>' on <site> ...` as each service thread starts; a hit prints the plaintext and the site it was found on. If no service knows the hash, the searches simply all come back empty.

### Stop at the first successful lookup

```
fang --exit-first --hash 7815696ecbf1c96e6894b779456d330e
```

Outcome: identical output, but the whole process terminates itself the moment one service returns a plaintext instead of waiting for the remaining threads.

### Process a file of hashes

```
fang --input hashlist.txt --threads 20
```

Outcome: every hash in the file (one per line) is checked against every configured service; matches print as they are found. First-match exit is ignored in list mode so later hashes still get their full sweep. `--threads` caps concurrent service threads (default 10).
