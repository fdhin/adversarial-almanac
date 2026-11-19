---
name: "xspy"
categories:
  - blackarch-keylogger
version: "1.0c"
homepage: "http://www.freshports.org/security/xspy/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-08"
build-system: other
license: GPL
meta-checked: "2026-11-09"
github-repo: "mnp/xspy"
github-stars: 31
github-pushed: "2018-03-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xspy

> Classic X11 keylogger that polls a (possibly remote) X server's keyboard state directly.

## What it's for

xspy (version 1.0c, credited to "JAM", unchanged since the late 1990s) exploits an oversight in the X Window System: instead of sniffing key events it polls the server's keymap with `XQueryKeymap` roughly every 1/10 second and prints which keys are held down. Because it reads keyboard state rather than events, it is not stopped by secure-mode keyboard grabs such as the xdm login window or xterm's secure mode, and it works against any X display that accepts the connection. The flip side is the defense: a server that does not grant access via xhost cannot be polled — run `xhost -` on the target to deny it. This is a display-level logger for open X networks; it says nothing about text consoles (that is logkeys territory). It is old C code but still builds against modern libX11.

## Typical use cases

### Monitor the local display

```
xspy
```

Outcome: connects to the default display and prints a continuous transcript of keys pressed on it to stdout, one keystroke burst per line; stop with Ctrl+C.

### Spy on a remote X display that permits connections

```
xspy -display 198.51.100.10:0
```

Outcome: polls the keyboard state of the X server at 198.51.100.10 and prints its keystrokes locally; this only works while that server grants access control entries to your host (check by running `xhost` on the target).

### Tune the polling rate and report key releases

```
xspy -display 198.51.100.10:0 -delay 100000 -up
```

Outcome: polls every 100000 microseconds (the default) and additionally prints up transitions for keys that report them, which can help distinguish repeats from held keys.

*No corpus evidence — derived from upstream documentation.*

GitHub: third-party mirror of the 1999 JAM upstream — Mitchell Perilstein's repackaged xspy 1.0c (same version and `-display`/`-delay`/`-up` CLI, extended with timestamped output).
