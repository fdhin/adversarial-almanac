---
name: "fstealer"
categories:
  - blackarch-automation
version: "0.1"
homepage: "http://packetstormsecurity.com/files/106450/FStealer-Filesystem-Mirroring-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fstealer

> Mirrors a remote Linux filesystem through an arbitrary file-disclosure bug by harvesting new file paths from everything it fetches.

## What it's for

fstealer (pleed, 2011) automates filesystem mirroring when you have an arbitrary-file-read primitive on a target — local file inclusion, path traversal, a leaking handler — but no command execution. It pulls each file from a seed path list through a pluggable "exploit" object, runs the contents through modules (an Apache-configuration parser and a procfs parser) that extract references to further files, and keeps fetching until no new paths turn up. It is a Python 2-era tool and ships only a local-reading exploit: to mirror a real remote host you implement the `FStealerExploit` interface (`initialize`/`getFile`) in the exploit module and select the class by name. It does not discover or exploit the disclosure bug for you.

## Typical use cases

### Dry-run the mirroring pipeline locally

```
fstealer target --odir /tmp/fstealer
```

Outcome: with the default local-reading exploit, files from the seed path list are read, processed by the Apache and procfs modules, and any newly referenced paths are queued in turn; the mirrored tree is written under `/tmp/fstealer`.

### Tune the crawl with a custom seed list, blacklist, and threads

```
fstealer target --pathlist /usr/share/fstealer/pathlist --blacklist /usr/share/fstealer/blacklist --threadnum 4 --odir /tmp/fstealer
```

Outcome: crawling starts from your seed list, skips anything matched by the blacklist, and fetches four files in parallel instead of one; the mirror accumulates in the output directory until the path queue is exhausted.

### Drive the mirror through your own file-read primitive

```
fstealer target --exploit MyLfiExploit --odir /tmp/fstealer-mirror
```

Outcome: once a `MyLfiExploit` subclass of `FStealerExploit` (implementing `initialize` and `getFile`) has been added to the exploit module, every fetch goes through your read primitive — e.g. the HTTP request that triggers the inclusion — so the remote filesystem, not the local one, is mirrored.

*No corpus evidence — derived from upstream documentation.*
