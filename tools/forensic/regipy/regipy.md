---
name: "regipy"
categories:
  - blackarch-forensic
version: "2.2.2"
homepage: "https://github.com/mkorman90/regipy"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "mkorman90/regipy"
github-stars: 279
github-pushed: "2026-11-05"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 146
gh-activity-checked: "2026-11-09"
---

# regipy

> Python library and CLI suite for parsing, diffing, and extracting forensic artifacts from offline registry hives.

## What it's for

regipy (by Maor Korman) parses Windows registry hives (REGF files: NTUSER.DAT, SYSTEM, SOFTWARE, SAM, UsrClass.dat, amcache.hve, ...) offline — no Windows host needed. Besides the Python API (`RegistryHive`, recursive subkey iteration, value reading), it ships command-line tools to inspect hive headers, dump an entire hive to JSON or a timeline, run its plugin battery (amcache, shimcache, computer name, persistence keys, ...), diff two hives of the same type, and apply hive transaction logs (.LOG1/.LOG2) to recover a dirty hive. Use it when you need registry parsing as a library, machine-readable bulk output, or hive-to-hive comparison; it does not mount live registries or modify the hives it reads (transaction-log replay writes a new recovered hive).

## Typical use cases

### Check a hive's header and cleanliness

See sequence numbers, timestamps, and whether transaction logs must be applied.

```
regipy-parse-header SYSTEM
```

Outcome: prints a table of the REGF header fields (signature, sequence numbers, last modification time, root key offset, ...) and warns `Hive is not clean! You should apply transaction logs` when the sequence numbers disagree.

### Dump a whole hive to JSON

Flatten every subkey and value for scripted post-processing.

```
regipy-dump NTUSER.DAT -o ntuser_dump.json
```

Outcome: walks the entire hive and writes a JSON document with all subkeys and values; add `-t` to emit a timeline instead. Large hives take a while.

### Run the plugin battery

Let hive-type detection pick the relevant forensic plugins.

```
regipy-plugins-run SYSTEM -o system_plugins.json
```

Outcome: the hive type is detected automatically and all relevant plugins (computer name, routes, services, ...) run against it, with results collected in the output JSON.

### Diff two hives

Find what changed between a baseline and a suspect hive.

```
regipy-diff NTUSER.dat NTUSER_modified.dat -o diff.csv
```

Outcome: prints/writes the detected differences — new subkeys, new or changed values with their timestamps and key paths — as a table or CSV, e.g. a new Run-key persistence entry.

### Replay transaction logs into a clean hive

Merge .LOG1/.LOG2 entries back into a hive that was captured dirty.

```
regipy-process-transaction-logs NTUSER.DAT -p NTUSER.DAT.LOG1 -s NTUSER.DAT.LOG2 -o recovered_NTUSER.dat
```

Outcome: writes `recovered_NTUSER.dat` with the committed-but-unflushed transactions applied; follow up with regipy-diff against the original to see what was recovered.

*No corpus evidence — derived from upstream documentation.*
