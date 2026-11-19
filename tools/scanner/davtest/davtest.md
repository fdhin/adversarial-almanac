---
name: "davtest"
categories:
  - blackarch-scanner
version: "3.a282c58"
homepage: "http://code.google.com/p/davtest/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "cldrn/davtest"
github-stars: 122
github-pushed: "2023-03-07"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# davtest

> Uploads test files for every web language to a WebDAV directory to learn which extensions it accepts and which actually execute — then optionally drops matching backdoors.

*No corpus evidence — derived from upstream documentation (davtest 1.2 README.txt at cldrn/davtest, the maintained upstream — lineage: Chris Sullo 1.0, Paulino Calderon 1.1, RewardOne 1.2; the BlackArch recipe still builds an older mirror of the same tool).*

## What it's for

davtest answers one question about a WebDAV-enabled directory: what can be PUT there, and what executes once it is there? It creates a test directory (MKCOL), PUTs a small test file for each supported language (php, asp, aspx, jsp, cfm, cgi, pl and more), verifies both the upload and — because each test file computes a value when run — whether it actually executes, optionally tries the PUT-as-`.txt`-then-MOVE/COPY-to-executable trick to bypass extension filters, and can finish by uploading real command-execution backdoors from its `backdoors/` directory for exactly the languages whose tests executed. It is a capability prober, not an exploit: results are a per-extension table of upload/execute succeed/fail plus URLs, printed to stdout, and nothing runs that you did not upload yourself.

## Typical use cases

### Test which file types a DAV directory accepts and executes

```
davtest -url http://target.example.com/webdav/ -auth user:password
```

Outcome: creates a `DavTestDir_*` folder, uploads per-language test files and prints the per-extension results — which types uploaded, which executed, and the URL for each executing test — ending with a summary naming the languages usable for further work. `-auth user:password` supplies credentials when the DAV share requires them (Basic/Digest only).

### Bypass extension filters with MOVE or COPY

```
davtest -url http://target.example.com/webdav/ -move
```

Outcome: test files are PUT as harmless `.txt` and then MOVE'd to the executable extension (`-copy` uses COPY instead); an extension that fails direct PUT but succeeds via `-move` reveals a filter that can be stepped around rather than a genuine restriction. `-nocreate` runs the same test at the `-url` level without creating the test directory.

### Plant backdoors for the executing languages

```
davtest -url http://target.example.com/webdav/ -sendbd auto
```

Outcome: for every language whose test executed, uploads the matching backdoor file from `backdoors/` and prints its location — `-sendbd ext` restricts planting to one extension. The backdoor stays in place after the run unless `-cleanup` is passed, which removes everything except the backdoor files.
