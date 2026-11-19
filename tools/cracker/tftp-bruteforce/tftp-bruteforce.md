---
name: "tftp-bruteforce"
categories:
  - blackarch-cracker
version: "0.1"
homepage: "http://www.hackingexposedcisco.com/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: LGPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tftp-bruteforce

> Fork-based TFTP filename bruteforcer for discovering config and IOS files on network equipment.

## What it's for

TFTP servers on network gear rarely use authentication — the secret is the filename. This tool hammers a TFTP server (UDP/69) with a wordlist of candidate filenames, forking one process per guess up to a concurrency limit, and reports which filenames actually exist on the server. It was written for auditing Cisco environments, where a discovered filename means a readable router/switch configuration or IOS image. TFTP has no credentials, so this is purely filename discovery: the wordlist quality (device hostnames, convention-based config names) determines success. It does not crack anything and does not work over protocols other than TFTP.

## Typical use cases

### Bruteforce filenames with the shipped wordlist

Guess which files a TFTP server exposes, using the packaged default wordlist.

```
tftpbrute 198.51.100.10 /usr/share/tftp-bruteforce/brutefile.txt 100
```

Outcome: prints the target, wordlist and process limit, then attempts each filename at up to 100 concurrent guesses; every existing file is reported with a `*** Found TFTP server remote filename : <name>` line — that filename is the one to fetch and review for credentials.

### Tune concurrency against an inetd-spawned daemon

Keep parallelism modest when the server forks per connection (e.g. xinetd), which caps concurrent transfers.

```
tftpbrute 198.51.100.10 cisco-configs.txt 10
```

Outcome: same filename sweep at 10 parallel guesses instead of 100; fewer dropped attempts on servers that limit connections, at the cost of runtime.

### Use a custom, target-specific wordlist

Fingerprint device hostnames first and guess names derived from them (usual config names plus the actual hostnames the server likely serves).

```
tftpbrute 198.51.100.10 target-filenames.txt 50
```

Outcome: enumerates only the candidate names you supplied; each hit is reported as a `*** Found` line naming a file that downloads successfully.

*No corpus evidence — derived from upstream documentation.*
