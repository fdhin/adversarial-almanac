---
name: "crackhor"
categories:
  - blackarch-cracker
version: "2.ae7d83f"
homepage: "https://github.com/CoalfireLabs/crackHOR"
packaged-by: [blackarch]
blackarch-builddate: "2014-04-26"
meta-checked: "2026-11-09"
github-repo: "CoalfireLabs/crackHOR"
github-stars: 55
github-pushed: "2021-04-06"
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

# crackhor

> Automated hash-cracking pipeline (crackHOR/Doozer): folder monitor, parallel hashcat sessions, and a Django web UI over a searchable hash database.

## What it's for

Coalfire's crackHOR, renamed Doozer in later revisions, automates the parsing, cracking and organization of password hashes. A watched folder accepts dropped hash files (pwdump-style output from SAM dumps or Responder captures); Doozer queues them into parallel cracking sessions that drive hashcat with per-hash-type attack patterns, and results accumulate in a database searchable through a Django web interface and a REST API. Attack modules cover NT, LM, NTLMv1 and NTLMv2 hashes, and the modular design allows other types to be plugged in. It orchestrates hashcat rather than implementing cracking itself, and it dates from the Python 2 / Django 1.5 era.

## Typical use cases

### Start the web interface and folder monitor

Bring up the full service on the cracking host.

```
./doozer/doozestop.py --startup
```

Outcome: launches the Django application together with the folder-monitoring daemon; hash files dropped into the watched directory are picked up, queued and cracked, with results appearing in the web interface.

### Crack a pwdump file in a named session

Feed a hash dump directly instead of using the drop folder.

```
./doozer/doozer.py -p hashes.pwdump -s sam-audit
```

Outcome: auto-detects the hash type from the file's contents, runs the matching hashcat attack patterns, writes a `.pot` file under the session directory, syncs cracked hashes into the database, and prints a `Cracked N passwords` summary.

### Import pre-cracked results

Load externally recovered password/hash pairs into the database.

```
./doozer/doozestop.py -a cracked.txt -f nt
```

Outcome: parses `password : hash` lines from cracked.txt and inserts them as NT-format entries so they show up in searches alongside session results.

### Export all recovered plaintexts

Collect everything the database has cracked so far.

```
./doozer/doozestop.py --plaintext
```

Outcome: prints the accumulated list of plaintext passwords stored in the Doozer database.

*No corpus evidence — derived from upstream documentation.*
