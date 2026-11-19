---
name: "kh2hc"
categories:
  - blackarch-crypto
version: "0.1.0.r2.g96b9e68"
homepage: "https://github.com/noraj/kh2hc"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/kh2hc"
github-stars: 7
github-pushed: "2026-04-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# kh2hc

> Convert HashKnownHosts-obfuscated OpenSSH known_hosts files into Hashcat-crackable hashes.

## What it's for

OpenSSH's `HashKnownHosts` option replaces hostnames in `known_hosts` with salted HMAC-SHA1 hashes. That hides hostnames from casual reading, but the salt is stored alongside the hash, so the entries are offline-brute-forceable. kh2hc parses a hashed known_hosts file and emits each entry as a `hash:salt` line in Hashcat format, turning a recovered known_hosts file into a wordlist attack that enumerates which hosts a user connects to. It also reports whether a known_hosts file is hashed at all. It ships as both a CLI (`kh2hc`) and a Ruby library (`Kh2hc`).

## Typical use cases

### Print Hashcat-format hashes to the terminal

```
kh2hc ~/.ssh/known_hosts
```

Outcome: prints one `<sha1_hash>:<salt>` line per hashed host entry, ready to be fed to Hashcat; the command fails cleanly on a known_hosts file that is not hashed.

### Write a Hashcat hash file directly

```
kh2hc ~/.ssh/known_hosts hosts.hashes
```

Outcome: writes the converted hashes to `hosts.hashes` instead of stdout so `hashcat hosts.hashes wordlist.txt` can run against it directly; `--no-color` and `--debug` adjust output colouring and argument display.

### Convert from Ruby code

```
ruby -e "require 'kh2hc'; puts Kh2hc.convert1('/home/user/.ssh/known_hosts')"
```

Outcome: returns the same `hash:salt` lines as a string via the library API, for use inside scripts; `Kh2hc.convert2` returns them as an array of objects instead.

*No corpus evidence — derived from upstream documentation.*
