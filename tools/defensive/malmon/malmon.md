---
name: "malmon"
categories:
  - blackarch-defensive
version: "0.3"
homepage: "http://sourceforge.net/projects/malmon/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# malmon

> Inotify watchdog for web-hosting servers that detects and quarantines uploaded exploits and backdoors in real time.

## What it's for

malmon (MALware MONitor, by ShadowX, 2011) is a small daemon for web-hosting and other file servers: it watches directories with inotify (via pyinotify) and checks every created, moved or modified file against two databases — MD5 checksums and hex signatures of known malware, exploits and PHP backdoors. Files below the configured size limit that match are moved to a quarantine directory (paranoid mode, the default) or only logged (warn mode); the definitions auto-update at startup and on a timer; and filenames on the blacklist are quarantined without any content check. Everything is driven by /etc/malmon/malmon.conf (watch dirs, quarantine path, regex excludes, size limit), and the running daemon is reachable through a UNIX socket. It is Python 2 software last released in 2011 and detects only known signatures — novel or obfuscated webshells pass through untouched.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Watch web roots in real time and quarantine matches

```
malmon
```

Outcome: starts the monitor per /etc/malmon/malmon.conf (set `watch_dir = /srv/www;/var/www` and `demonize = 0` to run it in the foreground); new or modified files matching the MD5/hex databases are moved to the quarantine directory (default /var/cache/malmon/infected) and logged to /var/log/malmon.log — `warn_only = 1` switches to log-only triage without moving files.

### Sweep existing files through the running daemon

```
malmon-scan /srv/www
```

Outcome: the bundled helper builds a `find /srv/www -type f` file list and submits every path over the daemon's UNIX socket for checking — the way to scan files that already exist rather than only new activity; it prints "malmon is not running" if the daemon is down.

### Sweep only recently changed files

```
malmon-scan /srv/www -mtime -2
```

Outcome: the arguments are passed straight to find(1) before the `-type f` filter, so standard find expressions select the file set — here only files modified within the last two days are queued for scanning, keeping re-checks of big trees cheap.
