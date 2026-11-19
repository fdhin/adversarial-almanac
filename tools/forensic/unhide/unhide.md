---
name: "unhide"
categories:
  - forensics
version: "20240510-1"
homepage: "https://github.com/YJesus/Unhide/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2025-08-06"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "YJesus/Unhide"
github-stars: 199
github-pushed: "2025-08-12"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# unhide

> Forensic tool that finds processes and TCP/UDP ports hidden by rootkits or LKMs.

## What it's for

Unhide cross-checks several independent views of a Linux system's process list — `/proc`, `ps` output, direct syscalls, procfs walking, and full PID-space bruteforcing — and reports PIDs that appear in one view but not another, i.e. processes hidden by a rootkit or loadable kernel module. Its companion `unhide-tcp` does the same for the network layer: it bruteforces all TCP/UDP ports and probes the ones absent from `ss`/`netstat` output to expose hidden listeners. Both must run as root. Known limits (upstream): on kernels newer than 2.6.33 the sysinfo-based checks may produce false positives (scheduler/cgroup effects), and the `quick` test is roughly 20x faster but noisier than the standard tests.

## Typical use cases

### Quick hidden-process check

Fast triage of a suspect host.

```
sudo unhide quick
```

Outcome: runs the combined proc/procfs/sys comparison and prints any PID that looks hidden. Exit status 1 means a hidden or fake thread was found, 0 means none — easy to wire into a monitoring check.

### Thorough multi-technique sweep

Maximum-coverage process hunt.

```
sudo unhide -m -d sys procall brute reverse
```

Outcome: runs the syscall, combined procfs, full PID-bruteforce and reverse checks, with `-m` adding deeper checks to the elementary tests and `-d` double-checking brute-force hits to avoid false positives — the deepest coverage at the cost of runtime.

### Hunt hidden listening ports

Check the network layer for concealment.

```
sudo unhide-tcp -fl
```

Outcome: bruteforces every TCP/UDP port, compares against `ss`/`netstat`, and for each hidden port found runs `fuser` (`-f`) and `lsof` (`-l`) to attribute it to an owning process. Add `-s` for a much faster scanning strategy on port-heavy systems, `-o` to write a dated log file.

*No corpus evidence — derived from upstream documentation (the single corpus mention is an unrelated "unhide hidden form fields" web-proxy menu item, not this tool).*
