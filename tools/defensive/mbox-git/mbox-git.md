---
name: "mbox-git"
categories:
  - defensive
version: "20140526.r274.a131424"
homepage: "http://pdos.csail.mit.edu/mbox"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: MIT
meta-checked: "2026-11-09"
github-repo: "tsgates/mbox"
github-stars: 666
github-pushed: "2018-06-21"
github-archived: false
github-issues: 22
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mbox-git

> Unprivileged sandbox that lets any user trial-run filesystem changes and commit only the good ones.

## What it's for

Mbox (MIT PDOS, "a lightweight sandbox for non-root users") runs a program in a sandbox without requiring root or special privileges: it interposes on system calls with ptrace (or seccomp/BPF with -s) and gives the program a layered view of the filesystem, so the program believes it is modifying the real system while all writes land in a private sandbox root. When the program exits, an interactive session lets you inspect what changed and selectively commit changes back to the real filesystem, ignore them, diff them, list them, or drop into a shell — a checkpointable trial run for installers, builds and unknown binaries. It can also cut network access (-n), fake a root environment (-R), and enforce per-path policy profiles (-p). It targets Linux; this package builds the 2014 development snapshot of the research prototype.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Trial-run an unknown program and review what it touched

```
mbox -- wget http://example.com/index.html
```

Outcome: runs the command under the sandbox; on exit it prints a network summary and the sandbox contents, then the commit prompt — [c]ommit, [i]gnore, [d]iff, [l]ist, [s]hell, [q]uit — where you choose which file changes (here the downloaded index.html) reach the real filesystem; the sandbox root defaults to a fresh /tmp/sandbox-* directory.

### Install packages into a fakeroot view

```
mbox -R -- apt-get install git
```

Outcome: emulates a fakeroot environment (-R), letting an unprivileged user run the package install; everything the installer writes stays in the sandbox for review instead of touching the host system.

### Run something with the network cut off

```
mbox -n -- ./untrusted.bin
```

Outcome: executes untrusted.bin with remote network accesses disabled while its filesystem writes stay sandboxed — combined isolation for a first look at an unfamiliar binary.

### Scope a build into a disposable directory

```
mbox -r outdir -- make
```

Outcome: roots the sandbox filesystem at outdir instead of an auto-created /tmp path, capturing the build's entire effect there for review or later reuse — a checkpointable build environment.

### Apply a per-path policy profile

```
mbox -p build.prof -- ./configure
```

Outcome: loads build.prof — a profile of per-path rules such as `allow: .` and `hide: ~` that decides which changes are permitted or hidden — replacing the interactive commit prompt with a declared policy.
