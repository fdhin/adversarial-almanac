---
name: "pspy"
categories:
  - blackarch-misc
version: "159.2312eed"
homepage: "https://github.com/DominicBreuker/pspy"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DominicBreuker/pspy"
github-stars: 6236
github-pushed: "2026-03-01"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# pspy

> Unprivileged Linux process snooping: watch other users' commands and cron jobs without root.

## What it's for

pspy shows commands run by other users — root's cron jobs, short-lived maintenance scripts, service-spawned processes — from an unprivileged shell. It needs no root and no debug privileges: it repeatedly scans `/proc` and places inotify watchers on directories such as `/usr`, `/tmp`, `/etc`, `/home`, `/var` and `/opt`, using file-system activity as a trigger to catch processes while they are alive. It is read-only observation: it does not elevate, inject, or modify anything, and extremely short-lived processes can still slip between scans. Reach for it during enumeration on a host where you have a low-privilege foothold and want to learn what root executes, with which arguments, and on what schedule.

## Typical use cases

### Watch for root cron jobs as an unprivileged user

Run the release binary with default settings and let it sit.

```
./pspy64
```

Outcome: prints a startup banner (`Watching recursively: [/usr /tmp /etc /home /var /opt]`), an initial line for every process already running, then a `CMD: UID=…  PID=… | <full command line>` line for each new process it observes. Left running for a few minutes, recurring root jobs (backup scripts, database dumps, housekeeping) scroll past with their complete argument strings — including scripts in `/root` or `/usr/local/*` you cannot read.

### Catch secrets passed on the command line

Run pspy in one terminal and trigger or wait for the leaking job in another (a database backup cron, a provisioning script, an SSH login hook).

```
./pspy64
```

Outcome: any process whose arguments embed credentials (e.g. `mysqldump -u root -pS3cret`, or a root script echoing a password into `passwd`) appears with the secret in cleartext. The two-session split is what makes this reliable: pspy keeps watching while the second session restarts the service or logs in to fire the command.

### Include file system events and a faster /proc scan

Turn on inotify event printing alongside command printing and scan `/proc` every second.

```
./pspy64 -pf -i 1000
```

Outcome: interleaves file-system events (files opened, created, modified in watched directories) with the `CMD:` process lines and raises the procfs scan rate to 1000 ms. Use when the interesting signal is file churn — a directory rewritten on a cycle, a config touched on every run — rather than just the command lines.

### Focus the watchers on specific directories

Replace the default watched trees with your own.

```
./pspy64 -r /usr/local/bin -r /opt/app -d /etc/cron.d
```

Outcome: inotify watchers are placed only on the listed paths — recursively for `-r`, single-level for `-d` — cutting event noise on busy hosts while file access under those paths still triggers the `/proc` scans that catch the responsible process. Watching `/usr` (especially its libraries) is the upstream-recommended default because most executions touch it.

### Learn a root job's schedule to race it

Watch long enough to see the same command appear several times.

```
./pspy64
```

Outcome: the timestamps on repeated `CMD: UID=0` lines give the job's interval (every 30 s, every 2 min, top of the hour), and the line gives the script path it runs. That answers the two questions a race needs: when root's next run fires, and which file (a writable script under `/tmp` or `/usr/local/bin`, a hijackable binary earlier in PATH) to replace just before it does.

### Map an application action to its shell command

Start pspy, then exercise the feature you suspect shells out.

```
./pspy64
```

Outcome: clicking the app's button, invoking the serverless function, or logging in via SSH surfaces the exact command the platform runs underneath — a `bash /opt/app/server-command.sh`, a `docker create …`, an executable in `/usr/local/sbin` fired on every login — confirming both the command path and the UID it runs as before you target the file it executes.

### Pick the right binary for a constrained target

Choose among the four release builds by architecture and transfer budget.

```
./pspy64s
```

Outcome: the `s` builds (`pspy32s`, `pspy64s`) are ~1 MB UPX-compressed, libc-linked binaries versus the ~4 MB fully static `pspy32`/`pspy64` — small enough for slow or size-limited transfers, while the static ones execute on any Linux regardless of installed libc. Use the 32-bit builds on older or embedded x86 targets.

Cross-links: [[anew]] and other text filters often consume pspy output; pair findings with file-replacement or PATH-hijack techniques on the host.
