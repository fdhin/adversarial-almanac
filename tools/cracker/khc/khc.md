---
name: "khc"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://packetstormsecurity.com/files/87003/Known-Host-Cracker-0.2.html"
packaged-by: [blackarch]
blackarch-builddate: "2016-02-16"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/khc"
github-stars: 0
github-pushed: "2019-05-04"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# khc

> Recovers hashed hostnames and IPs from OpenSSH known_hosts files.

*No corpus evidence — derived from the packaged source. The frontmatter repo is a third-party mirror (Seabreg/khc), verified byte-identical to the original 0.2 release tarball that BlackArch packages; khc itself (by rembrandt and benkei of helith) predates GitHub and was distributed via Packet Storm.*

## What it's for

OpenSSH can hash entries in a user's `known_hosts` (HMAC of the hostname with a random salt) so the hosts they connect to aren't directly readable. khc reverses those hashes by candidate testing: it walks IP ranges (its built-in private IPv4 ranges, a range you specify, or IPv6 with `-6`), a wordlist of hostnames, or a single name, and reports which candidates match each hashed entry. Use it after recovering someone's known_hosts file to reconstruct where they SSH to. It cannot break the hash mathematically — it only finds hosts you can enumerate candidates for — and it works purely offline against the file.

## Typical use cases

### Check all private IPv4 ranges against a known_hosts file

```
khc -f known_hosts -p
```

Outcome: tests every address of the private IPv4 space (192.168.x.x, 172.16-31.x.x, 10.x.x.x in that order) against each hashed entry and prints the plaintext hosts that match — an immediate map of the internal machines the file's owner connects to. Add `-o results.txt` to save matches to a file instead of stdout.

### Check a specific IP range

Distribute the work or target a subnet you already know about.

```
khc -f known_hosts -s 198.51.100.0 -e 198.51.100.255 -o results.txt
```

Outcome: brute-forces only the given start-to-end range, so several instances can each take a slice of the address space; matched hosts are written to `results.txt`. Add `-6` to work in IPv6 address space (both for `-s/-e` ranges and otherwise).

### Match a wordlist of hostnames

Reveal DNS names instead of addresses.

```
khc -f known_hosts -w hostnames.txt
```

Outcome: tests every hostname (one per line) against the hashed entries and reports the ones that hash to a stored value — useful when the target connects to named internal hosts rather than raw IPs. A single candidate can be checked with `-F hostname` without preparing a file; `-v` prints every attempt for debugging.

*No corpus evidence — derived from upstream documentation.*
