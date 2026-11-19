---
name: "phoss"
categories:
  - blackarch-cracker
version: "0.1.13"
homepage: "http://194.150.168.69/phoss"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-01"
build-system: make
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# phoss

> PHoss, "Phenoelit's own security sniffer" (1999): passively watches a network segment for cleartext logins — HTTP, FTP, LDAP, Telnet, IMAP4, VNC, POP3 — and prints the credentials as they pass by.

*No corpus evidence — derived from the packaged source (Phenoelit, phenoelit.de; still distributed from the author's site named in the frontmatter). The GitHub repo formerly linked in the frontmatter was an unrelated same-named Python project ("hyperparameter search simulation platform") and has been removed; the tool itself predates GitHub.*

## What it's for

Passive credential collection on a segment you can already observe. PHoss (the upstream binary is spelled `PHoss`; the package wraps it as `phoss`) listens with libpcap, identifies sessions both by destination port and by content patterns — each check can be toggled off with `-P` and `-p` — and reconstructs username/password pairs from cleartext protocol traffic as it flows past. It only helps where the traffic is in the clear: SSH, HTTPS and other encrypted logins are invisible to it. Typical placement is a mirror port, a tapped segment, or a box positioned on cleartext services during an authorized assessment. Snap length, interface and a tcpdump-style capture filter are all settable; on Ctrl-C it dumps the accumulated list of everything captured during the session.

## Typical use cases

### Sniff cleartext logins on one interface

```
phoss -i eth0
```

Outcome: prints the program banner, then for every cleartext login observed prints the protocol and the recovered credentials as they are captured; Ctrl-C stops the capture and dumps the accumulated list.

### Restrict processing to specific traffic

```
phoss -i eth0 -f 'port 21 or port 110'
```

Outcome: the pcap filter (tcpdump syntax) limits processing to FTP and POP3 traffic so only those credentials get parsed — useful to cut noise on busy links; add `-p` or `-P` when pattern- or port-based session identification produces false hits.

### Log captures line-buffered to a file

```
phoss -i eth0 -L | tee phoss.log
```

Outcome: `-L` makes stdout line-buffered so each find is flushed immediately instead of sitting in a block buffer, giving a running log you can grep while the capture continues.
