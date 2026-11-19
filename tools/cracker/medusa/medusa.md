---
name: "medusa"
categories:
  - passwords
  - web
version: "2.2-13"
homepage: "http://www.foofus.net/jmk/medusa/medusa.html"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-12"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "jmk-foofus/medusa"
github-stars: 899
github-pushed: "2025-05-14"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# medusa

> Speedy, parallel, modular login brute-forcer for network services — the Foofus Medusa, not the same-named e-commerce platform.

## What it's for

Medusa (Joe Mondloch, Foofus Networks) is a thread-based parallel login auditor: it brute-forces remote authentication across many hosts, users and passwords concurrently. Each supported service — SMB (v1–3 with signing), SSHv2, HTTP forms, MS-SQL, POP3, RDP, FTP, and many more — is an independent `.mod` module selected with `-M`, so the list grows without touching the core. Targets, usernames and passwords can each be a single value or a file, or come from a colon-separated combo file (PwDump output is accepted too). It is a credential-audit tool for services you are authorized to test; it does not crack offline hashes. Do not confuse it with the Medusa JavaScript e-commerce framework (medusajs) or the Medusa HTTP server embedded in supervisord — same name, unrelated software.

## Typical use cases

### Audit one account against a password list

```
medusa -h 198.51.100.10 -u administrator -P passwords.txt -e ns -M smbnt
```

Outcome: one `ACCOUNT CHECK: [smbnt] ...` line per attempt; `-e ns` additionally tries an empty password and the username as the password before the list.

### Spray users and passwords across many hosts in parallel

```
medusa -M ssh -H hosts.txt -U users.txt -P passwords.txt -T 20 -t 10 -L -F
```

Outcome: tests up to 20 hosts concurrently with 10 login attempts each; `-L` parallelizes by user (each thread takes a distinct user per host) and `-F` aborts the entire audit on the first valid credential found anywhere (`-f` is the per-host variant — stop testing that host only).

### Drive targets from a combo file

```
medusa -M smbnt -C combo.txt
```

Outcome: reads `host:user:password` lines from combo.txt (empty fields fall back to `-H`/`-U`/`-P` globals); a PwDump-style file (`user:id:lm:ntlm:::`) is detected automatically.

### Resume an interrupted scan

```
medusa -M ssh -H hosts.txt -U users.txt -p password -Z h2u3u4h3.
```

Outcome: continues the scan from where a previous SIGINT stopped it, using the resume token that the aborted run printed in its `ALERT` line.

### Point a module at a specific target path

Pass module-specific options with `-m`.

```
medusa -h 198.51.100.10 -u admin -P passwords.txt -M http -m DIR:/protected
```

Outcome: `-m KEY:VALUE` forwards a parameter straight to the selected module — here the HTTP module authenticates against `/protected` instead of its default `/`. The same mechanism configures other modules (`-m NETBIOS -n 139` for smbnt, custom headers via `-m CUSTOM-HEADER:'Cookie: ***'` for http); run `medusa -M <module> -q` to print the options a module accepts.

*No corpus evidence — derived from upstream documentation.*
