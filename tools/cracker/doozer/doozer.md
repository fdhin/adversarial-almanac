---
name: "doozer"
categories:
  - blackarch-cracker
version: "9.5cfc8f8"
homepage: "https://github.com/Coalfire-Research/Doozer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Coalfire-Research/Doozer"
github-stars: 55
github-pushed: "2021-04-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# doozer

> Automated Windows-hash cracking platform: a watch-folder worker feeds hashcat/ophcrack while a Django web UI tracks sessions and results.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Doozer (Coalfire Research, Python 2) automates the boring half of credential cracking: parsing dumped hashes, picking the right attack, running it and filing the results. It ingests NT/LM/NTLMv1/NTLMv2 material from pwdump-style files or Responder output, cracks it through ophcrack and hashcat under the hood, and records everything in a database fronted by a Django web interface (live session view, hash search) and a REST API. A monitor watches a configured folder, so dropping a hash file there queues a cracking session automatically; parallel session count and queueing are set in `doozer/core/sethor.py`. It is not an attack tool by itself — it organizes cracking of hashes you already have, and its hash-type coverage is limited to the Windows family.

## Typical use cases

### Crack a pwdump export in one shot

Hand a dump straight to the worker CLI.

```
sudo doozer -p dump.pwdump -s assessment-01
```

Outcome: doozer reads the pwdump-format file, auto-detects the hash type among NT/LM/NTLMv1/NTLMv2, runs the matching cracking engine, writes per-session `.pot` files, pushes cracked hashes into the Doozer database and prints the number of recovered passwords. `-p` is required; `-s` names the session (used for the working directory and logs).

### Start the platform

Bring up the web application and the watch-folder monitor together.

```
sudo doozestop --startup
```

Outcome: launches the Django web interface and the folder monitor; any hash file dropped into the watched directory (configured in `doozer/core/sethor.py`) is moved to a working directory and queued for a cracking session, visible in the web UI as it runs.

### Check connectivity to a running Doozer

Verify the utility can reach the server before scripting against it.

```
doozestop --test
```

Outcome: contacts the Doozer host/port from `sethor.py` and reports whether the platform answers — use it after `--startup` or before importing data.

### Import existing cracked credentials

Feed a password:hash list gathered elsewhere back into the database.

```
sudo doozestop -a cracked.txt -f nt
```

Outcome: reads `cracked.txt` lines of the form `password : hash`, converts them for the chosen format (`nt` or `lm`) and inserts them into the Doozer database so they show up in searches and session results. `-p [file]` does the same for a bare list of plaintext passwords.

### Dump everything recovered

Pull the accumulated plaintext out of the database.

```
doozestop --plaintext
```

Outcome: prints every plaintext password stored across sessions — a ready-made wordlist of what this platform has already recovered. `doozestop -k` stops the platform and `--archive` retires finished jobs.
