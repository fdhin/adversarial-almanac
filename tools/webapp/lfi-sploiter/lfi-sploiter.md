---
name: "lfi-sploiter"
categories:
  - blackarch-webapp
version: "1.0"
homepage: "http://packetstormsecurity.com/files/96056/Simple-Local-File-Inclusion-Exploiter.0.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# lfi-sploiter

> Simple Local File Inclusion Exploiter — pass one URL and one parameter; it scans for LFI, works out the traversal depth, and dumps interesting files to disk.

## What it's for

lfi-sploiter (Python 2, by Valentin Hoebel, 2010, distributed via Packet Storm) is a deliberately minimal LFI tool with exactly two flags: the target URL (with `http://`) and the parameter to test. If you do not yet know whether the parameter is vulnerable, it scans it and tells you; if it is, it determines how the inclusion can be exploited (directory depth, null-byte support), then dumps a list of interesting files (`/etc/passwd`, logs, configuration files) from the *nix target to your hard disk. It handles only "simple" LFI — single-parameter inclusions without filters — no SEO URLs, no Windows targets, and no exploitation beyond file reading. For fuzz-plus-exploit menus see the siblings [[lfi-fuzzploit]] and [[lfi-exploiter]]; for payload crafting see [[lfi-image-helper]]; for PHP input/filter/data-URI shells see [[lfifreak]].

## Typical use cases

### Scan and exploit one parameter

The single supported workflow.

```
lfi-sploiter --exploit-url="http://target.example.com/page.php?file=main" --vulnerable-parameter="file"
```

Outcome: connects with a randomized User-Agent, first checking the parameter for LFI (it reports whether the parameter is vulnerable), then works out the working traversal depth and null-byte behavior, and dumps its interesting-file list to files on disk — check your working directory for the recovered `passwd` and log contents. Pass only one parameter at a time; a parameter you already know is vulnerable is attacked directly.

### Read the built-in help

Everything else the tool will tell you.

```
lfi-sploiter --help
```

Outcome: prints the tool's own description, usage, feature list (random user agent, connection check, scanner, depth/null-byte detection, file dumping) and its usage notes — the authoritative summary of what this minimal tool does and does not support.

No corpus evidence — derived from the packaged upstream source (Packet Storm `lfi_sploiter.py.txt`; getopt flags `--exploit-url`/`--vulnerable-parameter` and help text read from the script; BlackArch installs it as `/usr/bin/lfi-sploiter` under Python 2).
