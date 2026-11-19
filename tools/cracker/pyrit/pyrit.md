---
name: "pyrit"
categories:
  - blackarch-cracker
version: "0.5.0"
homepage: "https://github.com/JPaulMora/Pyrit"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-12-26"
build-system: other
license: GPL3
meta-checked: "2026-11-09"
github-repo: "JPaulMora/Pyrit"
github-stars: 1137
github-pushed: "2026-08-31"
github-archived: false
github-issues: 153
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# pyrit

> Precomputes WPA/WPA2-PSK Pairwise Master Keys into a database, then attacks captured handshakes at millions of guesses per second.

## What it's for

Pyrit exploits the fact that a WPA/WPA2-PSK Pairwise Master Key depends only on the ESSID and the password: it imports password dictionaries, precomputes the (expensive, PBKDF2-HMAC-SHA1) PMK for every password × ESSID pair into a local database, and then attacks captured four-way handshakes from that database — turning most of the per-guess cost into a one-time precomputation. Compute scales across CPU cores and GPU backends (cpyrit_cuda, cpyrit_opencl, and AMD cards via [[cpyrit_calpp]]), and multiple machines can share one database through its relay/serve mode. It parses pcap captures directly (including gzipped ones) and can export/import cowpatty-format files. Note this is the classic Python 2.7 line; the upstream project also publishes Pyrit3, a from-scratch Python 3 rewrite — for new installs prefer that or check compatibility first. Pyrit does not capture handshakes itself; bring your own capture file.

## Typical use cases

### Check a capture file for usable handshakes

Before any cracking, verify the capture actually contains a complete four-way handshake.

```
pyrit -r capture.cap analyze
```

Outcome: lists every access point (BSSID, ESSID) and station in the capture, marking each station with `handshake found` or not; only a found handshake is attackable, and unneeded frames can later be stripped with the `strip` command to shrink the file.

### Populate the database and precompute PMKs

Load a dictionary for an ESSID and let all cores grind through the precomputation.

```
pyrit -i words.txt import_passwords
pyrit -e HomeNetwork create_essid
pyrit -e HomeNetwork batch
```

Outcome: `import_passwords` stores the wordlist, `create_essid` registers the target network name, and `batch` computes PMKs for every password × ESSID pair with per-core progress bars and PMKs/s rates; interrupted runs resume where they left off.

### Attack a handshake from the database

Test a captured handshake against everything precomputed.

```
pyrit -r capture.cap -e HomeNetwork attack_db
```

Outcome: Pyrit reads the handshake, matches it against the stored PMKs (typically over a million passwords per second) and prints `The password is '<password>'` when a match exists, otherwise reports the password was not found.

### One-shot attack without storing anything

Check a single wordlist against a handshake with no database, e.g. on a live system with little storage.

```
pyrit -r capture.cap -e HomeNetwork -i words.txt attack_passthrough
```

Outcome: computes PMKs on the fly while streaming the wordlist and prints the password if present in the list; nothing is written to the database, so re-running costs the full computation again.

### Benchmark and enumerate compute cores

See what hardware Pyrit will use and how fast it is.

```
pyrit list_cores
pyrit benchmark
```

Outcome: `list_cores` prints every compute core (CPU cores plus any GPU modules loaded — missing GPUs mean the backend module isn't built or enabled in `~/.pyrit/config`); `benchmark` calibrates briefly and reports PMKs/s per core so you can compare CPU vs GPU throughput.

*No corpus evidence — derived from upstream documentation.*
