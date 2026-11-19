---
name: "hashkill"
gentoo-category: "app-crypt"
version: "9999"
homepage: "http://www.gat3way.eu/hashkill"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: autotools
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "gat3way/hashkill"
github-stars: 197
github-pushed: "2014-02-04"
github-archived: false
github-issues: 18
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hashkill

> Multi-threaded, GPU-accelerated offline password cracker with a plugin per hash format

## What it's for

hashkill recovers passwords from captured hashes offline. It loads a file of hashes and attacks them with one of four modes — Markov (the statistical default), pure brute force, a rule-based generator (the hashgen rule language), and resumable sessions — using SSE2/AVX-accelerated CPU code paths or OpenCL on AMD and NVIDIA GPUs, including mixed multi-GPU systems. More than 30 plugins cover formats from MD5 and SHA1 through WPA handshakes, passworded RAR archives, MS Office documents and TrueCrypt volumes; `-P` lists what the build supports. It never talks to a network service, so it complements online logon crackers such as hydra rather than overlapping them.

## Typical use cases

### List the supported hash plugins

Check which formats this build can crack before choosing a plugin.

```
hashkill -P
```

Outcome: prints a summary of every available hash plugin; `hashkill --plugin-info=md5` prints detailed information about a single plugin such as its expected hash input format.

### Crack a hash file with the default Markov attack

Run the statistical attack against a list of hashes.

```
hashkill -f hashes.txt -p md5 -o cracked.txt
```

Outcome: loads hashes.txt (one hash per line) with the md5 plugin and starts the default Markov attack using the rockyou statfile; cracked plaintexts stream to the console and the full set is written to cracked.txt on completion or interrupt. `-c` forces CPU use even when GPU acceleration is available.

### Brute force with a charset ruleset

Exhaustively try candidates from a length range and character set.

```
hashkill -f hashes.txt -p sha1 -b 1:8:lalphanum
```

Outcome: runs full brute force generating candidates of length 1-8 over lowercase letters and digits. Rulesets have the form `start:end:set[:extra-chars]` with predefined sets `alpha`, `ualpha`, `lalpha`, `lalphanum`, `ualphanum`, `alphanum`, `ascii` — for example `-b1:2:lalpha:*` adds the asterisk to lowercase letters.

### Tune a Markov attack

Load a specific statfile and control candidate space and length.

```
hashkill -f hashes.txt -p md5 -m rockyou -n 12 -N 10
```

Outcome: runs the Markov attack against the rockyou statfile with threshold 12 (lower values mean more candidates and slower runs) and a maximum candidate length of 10. `-M` lists the statfiles available to `-m`.

### Resume an interrupted session

Continue a crashed or aborted cracking run from its checkpoint.

```
hashkill -S
```

Outcome: lists saved cracking sessions (hashkill auto-checkpoints every few seconds); `hashkill -s <sessionid>` reloads that session and resumes cracking from the last checkpoint.

*No corpus evidence — derived from upstream documentation.*
