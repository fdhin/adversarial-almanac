---
name: "plumber.py"
categories:
  - blackarch-misc
version: "18.3f1be68"
homepage: "https://github.com/cybereason/linux_plumber"
packaged-by: [blackarch]
blackarch-builddate: "2021-12-14"
meta-checked: "2026-11-09"
github-repo: "cybereason/linux_plumber"
github-stars: 79
github-pushed: "2019-06-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# plumber.py

> Root-run ftrace wrapper that live-streams process execution events (fork, execve, exit).

## What it's for

Plumber is a single-script Python wrapper around Linux ftrace that watches the whole system for short-lived process events — fork() calls, execve() calls and process terminations — and prints them as a colorized live feed. Its purpose is catching one-shot executions that `ps` and `top` never show because the process is gone before you look: a cron-driven interpreter, a dropper, or a tool spawned on a timer. It needs a kernel with ftrace support and must run as root to read the trace pipe. It only observes and prints events; it does not filter by user, log to a file, or take action on matches.

## Typical use cases

### Confirm ftrace support before running

Check the kernel prerequisite first.

```
cat /proc/sys/kernel/ftrace_enabled
```

Outcome: prints `1` when the kernel exposes ftrace, which Plumber requires; `0` or a missing file means tracing will not work.

### Live-monitor process executions system-wide

Watch every process event on the host as it happens.

```
sudo python plumber.py
```

Outcome: a continuously updating, colorized console feed of every fork, execve and process-exit event on the system — watch for an interpreter or utility spawning from an unexpected parent, even when the process exits within milliseconds.

*No corpus evidence — derived from upstream documentation.*
