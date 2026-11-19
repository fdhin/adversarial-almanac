---
name: "fdsploit"
categories:
  - blackarch-webapp
version: "26.4522f53"
homepage: "https://github.com/chrispetrou/FDsploit"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "chrispetrou/FDsploit"
github-stars: 273
github-pushed: "2021-03-24"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fdsploit

> Automatic LFI/RFI and directory-traversal fuzzing, enumeration and exploitation.

## What it's for

FDsploit (by Christoforos Petrou, "game0ver") takes a URL or a list of URLs and probes their parameters for local/remote file inclusion and directory traversal bugs. It supports payload depth control, URL-encoding, null-byte and `?` termination to strip suffixes, base64-encoded payloads, keyword matching in responses, cookies, proxies, random user agents and POST parameters. When a local file inclusion is confirmed it can drop into one of three pseudo-shells: a plain file reader (`simple`), command execution through PHP's `expect://` wrapper (`expect`), or through `php://input` (`input`). The upstream repository has been archived since 2021 — the tool still runs but is unmaintained.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Audit a single URL

Check one suspect parameter for inclusion bugs.

```
fdsploit -u 'http://target.example.com/page.php?file=PARAM'
```

Outcome: probes the parameter with file-inclusion and traversal payloads (either `-u` or `-f` for a URL list is required) and prints any findings together with the payload that worked.

### Tune traversal depth, termination and confirmation

Adapt the attack to filtered or suffixed includes.

```
fdsploit -u 'http://target.example.com/page.php?file=PARAM' -d 8 -t '%00' -k 'root:'
```

Outcome: climbs up to 8 directory levels (`-d`, default 5), terminates payloads with a null byte (`-t`) to defeat appended suffixes, and only reports responses containing `root:` (`-k`) — the classic `/etc/passwd` confirmation string.

### Drop into an LFI pseudo-shell

Escalate a confirmed inclusion to file read or command execution.

```
fdsploit -u 'http://target.example.com/page.php?file=PARAM' --lfishell expect
```

Outcome: once exploitation is possible, opens a semi-interactive shell running commands through PHP's `expect://` wrapper; `simple` gives a file-only reader and `input` routes through `php://input`.
