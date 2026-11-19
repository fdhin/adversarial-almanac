---
name: "logkeys"
categories:
  - blackarch-keylogger
version: "97.98aac72"
homepage: "https://github.com/kernc/logkeys"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-02-01"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kernc/logkeys"
github-stars: 837
github-pushed: "2025-07-27"
github-archived: false
github-issues: 72
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# logkeys

> Userspace GNU/Linux keylogger that reads the input subsystem's event devices and records keystrokes to a log file.

## What it's for

logkeys is a Linux keylogger by kernc that works entirely in userspace: instead of a kernel module it relies on the event interface of the Linux input subsystem (`/dev/input/event*`), so it needs root but no compiled-in kernel component. It logs all common character and function keys, is aware of Shift and AltGr modifiers, and runs as a daemon writing to `/var/log/logkeys.log` by default (a file readable only by root). It does not see keystrokes typed into a remote X session — for that a display-level tool is needed. The package also ships two setuid helper scripts, `llk` and `llkk`, to start and stop the daemon without a root password. Requires a UTF-8 terminal locale to display captured characters correctly.

## Typical use cases

### Start the daemon and watch what it captures

```
sudo logkeys --start --output test.log
```

Outcome: forks the logging daemon, which appends every keypress (after a `Logging started...` header) to `test.log`; follow it live from a second terminal with `tail --follow test.log` and compare against what you type.

### Log with the default US layout

```
sudo logkeys --start -u
```

Outcome: starts the daemon with the built-in US keymap (skips custom keymap loading) and writes to the default log file `/var/log/logkeys.log`.

### Build a keymap for a non-US layout and use it

```
logkeys --export-keymap my_lang.keymap
sudo logkeys --start --keymap my_lang.keymap
```

Outcome: the first command dumps the currently loaded keymap to `my_lang.keymap` (edit it in a UTF-8 editor to fix mappings, best done from a plain virtual console); the second restarts the daemon using that keymap so Shift and AltGr combinations are translated correctly.

### Pin the input device when autodetection fails

```
sudo logkeys --start --device /dev/input/event3 --output test.log
```

Outcome: logs only from the named event device — the fix for an empty log or `Error opening input event device` when logkeys guesses the wrong device id. Find the right one by running `cat /dev/input/eventX` as root and typing: the device that produces output is the keyboard.

### Stop the daemon

```
sudo logkeys --kill
```

Outcome: terminates the running logger (equivalent to the shipped `llkk` helper) and appends a `Logging stopped...` marker to the log file.

*No corpus evidence — derived from upstream documentation.*
