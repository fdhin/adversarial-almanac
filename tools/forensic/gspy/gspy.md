---
name: "gspy"
categories:
  - blackarch-forensic
version: "0.1.1"
homepage: "https://github.com/Mutasem-mk4/gspy"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-21"
build-system: go
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Mutasem-mk4/gspy"
github-stars: 1
github-pushed: "2026-10-10"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 128
gh-activity-checked: "2026-11-09"
---

# gspy

> Attach eBPF probes to a live Linux Go process and map every syscall back to the goroutine that triggered it.

## What it's for

gspy (Mutasem Kharma) is an eBPF-based forensic inspector for running Go binaries. Tools like strace see only OS threads, which hides which internal Go operation caused a syscall; gspy hooks the Go scheduler with uprobes to track goroutine IDs and joins them with syscall tracepoints, then reads the executable's ELF symbols and Go PC table to resolve frames — including on stripped binaries. It needs Linux 5.8+ with BPF support and elevated privileges (root, or `cap_bpf,cap_perfmon`), refuses Go runtime versions it cannot resolve offsets for rather than guessing, and imposes real tracing overhead. It observes an existing process; it does not dump or alter its memory.

## Typical use cases

### Inspect a running Go process live

Attach to a suspect service by PID.

```
gspy 1234
```

Outcome: a terminal UI opens showing goroutines mapped to their recent syscalls with counts and latencies; press `q` to detach. `--top` (default sort) orders by total syscall volume, `--latency` by the worst syscall blocking.

### Isolate network or I/O activity

Filter the view to one syscall class.

```
gspy 1234 --filter net
```

Outcome: only socket-related syscalls (and their goroutines) are shown — `io`, `net`, `sched` and `all` are the accepted modes; generic descriptor calls such as `poll`/`epoll` count as `io`, since they do not prove a socket.

### Export events for pipeline analysis

Stream events as JSON instead of watching the TUI.

```
gspy 1234 --json > events.jsonl
```

Outcome: one JSON object per syscall event (goroutine ID, syscall, latency in microseconds, kernel timestamp) written to `events.jsonl`, suitable for `jq` filtering or SIEM import; `--filter` applies to the stream as well.

### Record execution integrity for a forensic session

Snapshot what was traced before detaching.

```
gspy 1234 --readonly
```

Outcome: the executable file's SHA-256 is recorded and events are marked as readonly-captured — useful for documenting which binary was under observation; it is a file hash, not an attestation of process memory.

*No corpus evidence — derived from upstream documentation.*
