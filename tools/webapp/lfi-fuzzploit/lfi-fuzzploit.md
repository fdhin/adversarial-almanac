---
name: "lfi-fuzzploit"
categories:
  - blackarch-webapp
version: "1.1"
homepage: "http://packetstormsecurity.com/files/106912/LFI-Fuzzploit-Tool.1.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# lfi-fuzzploit

> Menu-driven Python 2 toolkit that fuzzes for LFI, dumps interesting files, and escalates via /proc/self/environ, file descriptors or poisoned logs — with an HTML report per scan.

## What it's for

LFI Fuzzploit (Python 2, by nullbyt3, 2011, distributed via Packet Storm) is a twelve-option interactive menu covering the whole LFI workflow against Linux PHP targets: fuzz a parameter with a large traversal/evasion payload set (null bytes, encoding tricks, filter bypasses), dump a curated list of interesting system files (`/etc/passwd`, `sshd_config`, `/proc/self/*`, config files), exploit the inclusion via `/proc/self/environ`, Apache log poisoning or the `/proc/self/fd/` file descriptors, and even read streams through `php://` wrappers. Scans write an `lfi_fuzz*.html` report of what was found. It predates modern PHP hardening — its exploitation routes assume permissive legacy settings — and it takes the target interactively, one URL at a time. Siblings in this vault: [[lfi-exploiter]] (single-flow environ/log chain), [[lfi-sploiter]] (parameter scanner+dumper), [[lfi-image-helper]] (image payload crafting), [[lfifreak]] (PHP input/filter/data-URI shells).

## Typical use cases

### Fuzz a parameter for LFI and traversal

Menu option 1 — find the inclusion first.

```
lfi-fuzzploit
# option 1 -> "Site and uri to Fuzz: http://target.example.com/page.php?file="
# -> nullbyte/evasion: y
```

Outcome: fires the built-in payload set (dozens of traversal encodings, `....//`, `%2e%2e`, `%c0%af`, null-byte variants) at the parameter and writes the results to `lfi_fuzz.html` in the working directory — open it to see which payload shapes produced file content, then feed the working traversal into the exploitation options.

### Exploit via /proc/self/environ

Menu option 4 — classic User-Agent PHP injection.

```
lfi-fuzzploit
# option 4 -> "Site and uri to attack?: http://target.example.com/page.php?file="
# -> nullbyte: n
```

Outcome: the tool includes `/proc/self/environ` with a PHP payload injected in the request headers and reports whether the code executed (requires the environ file readable and `magic_quotes` off, as its own prompt warns); success gives command execution through the inclusion.

### Include or poison Apache logs

Menu options 6 and 7 — log-based routes.

```
lfi-fuzzploit
# option 6 (include known apache logs) or 7 (exploit via logfile)
# -> "Site and uri to attack?: http://target.example.com/page.php?file="
```

Outcome: option 6 tries the built-in list of known access/error log paths directly; option 7 goes further and poisons a log with PHP before including it, attempting execution — both report each candidate path and its result on screen. Options 3 and 5 cover the `/proc/self/fd/` file-descriptor variants (option 5 prompts for the fd number, 1-12).

No corpus evidence — derived from the packaged upstream source (Packet Storm `LFI_fuzzploit-1.1.tar.gz`; menu options, prompts and HTML report filenames read from `lfi_fuzzploit`; BlackArch installs it as `/usr/bin/lfi-fuzzploit`).
