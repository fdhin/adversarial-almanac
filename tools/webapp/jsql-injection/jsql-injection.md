---
name: "jsql-injection"
categories:
  - blackarch-webapp
version: "0.115"
homepage: "https://github.com/ron190/jsql-injection"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-06-29"
build-system: scripts
license: GPL-2.0-or-later
aliases: ["jsql"]
meta-checked: "2026-11-09"
github-repo: "ron190/jsql-injection"
github-stars: 1788
github-pushed: "2026-08-08"
github-archived: false
github-issues: 57
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 171
gh-activity-checked: "2026-11-09"
---

# jsql-injection

> Java GUI that automates SQL injection end to end: strategy identification, database dumps, admin-page discovery, file reads, exploit creation and offline hash cracking.

## What it's for

jSQL Injection is a cross-platform Java desktop application (a GUI, not a CLI) for finding and working database information out of a server. You give it a target URL; it validates the target, identifies the injection strategy (engine and technique can also be pinned manually), and then the tabs take over: enumerate databases/tables/columns/rows, hunt for admin pages, read server files through the injection, create file-upload/webshell exploits, batch-check lists of URLs, brute-force hashes and encode/decode strings. The `Brute force` and `Encoding` tabs need no target; every other tab applies to whatever sits in the address bar. It is a workbench for a target you already suspect, not a vulnerability scanner, and it needs a Java 21-25 runtime.

## Typical use cases

### Launch the GUI

Start the window; the packaged wrapper simply runs the jar.

```
jsql-injection
# equivalent:
java -jar /usr/share/jsql-injection/jsql-injection-v0.115.jar
```

Outcome: the Swing window opens with the target address bar and the tabs `Database`, `Admin page`, `Read file`, `Exploit`, `Brute force`, `Encoding` and `Batch scan`. Everything else in the tool happens from these tabs.

### Dump a database through the injection tab

Validate the target and enumerate its data.

```
jsql-injection
# GUI: address bar -> http://target.example.com/product.php?id=1 -> Database tab
```

Outcome: jSQL validates the URL, identifies the database engine and injection strategy, and loads the database tree on the left; select a database and table, then Right click + Load on the table to pull its rows into a tab on the right where they can be searched, sorted and exported. The engine/strategy can be forced from the address bar when auto-identification picks wrong.

### Check a list of targets for injectability

Run the same identification across many URLs.

```
jsql-injection
# GUI: Batch scan tab -> add URLs -> start
```

Outcome: each target in the list is probed in turn and tagged with the injection strategy identified for it — the fast way to triage a fleet before dumping anything.

### Read a server file through the injection

Leverage DBMS file-read rights.

```
jsql-injection
# GUI: inject target first -> Read file tab -> pick path -> Read
```

Outcome: if the current database user has sufficient rights, the chosen file path is read via the injection and opened in a tab; otherwise the read fails cleanly. Works only after the target has been successfully injected in the Database tab.

### Find admin pages and crack found hashes

Two target-independent helpers round out a session.

```
jsql-injection
# GUI: Admin page tab -> scan target list; Brute force tab -> hash + charset
```

Outcome: the Admin page tab requests a list of common admin paths against the current target and loads any page that returns a successful HTTP response in a tab; the Brute force tab computes hashes over a chosen characters range offline and shows the original text when a computed hash matches — useful for credentials pulled from a dumped table.

No corpus evidence — derived from upstream documentation (ron190/jsql-injection README and wiki General page; packaged launcher `java -jar`).
