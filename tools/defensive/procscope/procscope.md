---
name: "procscope"
categories:
  - blackarch-defensive
version: "1.1.0"
homepage: "https://github.com/Mutasem-mk4/procscope"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-21"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "Mutasem-mk4/procscope"
github-stars: 52
github-pushed: "2026-10-04"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 186
gh-activity-checked: "2026-11-09"
---

# procscope

> eBPF tracer that scopes runtime investigation to a single process tree.

## What it's for

procscope launches a command (or attaches to an existing PID or process name) and traces what that process tree actually does at runtime — process exec/fork/exit, file operations, network connections, and privilege transitions — using CO-RE eBPF on Linux kernels 5.8+ with BTF. It automatically follows forks but stays scoped to the target tree, unlike whole-system tracers, and produces incident-response-ready output: a live color-coded timeline, JSONL event streams, and structured evidence bundles with a Markdown summary. Built for malware triage, suspicious-binary investigation, and debugging; explicitly not an EDR, SIEM, or whole-system tracing replacement, and eBPF observation has overhead and may lose events under load. Requires root.

## Typical use cases

### Run a suspicious binary under observation

Execute a sample and watch its behavior live.

```
sudo procscope -- /path/to/sample
```

Outcome: The binary runs under tracing while a live timeline prints rows of `TIME PID COMM EVENT DETAILS` — exec, file.open, net.connect, priv.setuid and similar — with security-relevant events flagged.

### Capture a full evidence bundle

Produce the artifact set an IR team expects.

```
sudo procscope --out case-001 --summary report.md -- /path/to/sample
```

Outcome: Writes `case-001/` containing metadata.json, events.jsonl, process-tree.txt, files.json, network.json, notable.json, plus a summary.md report; files are root-owned mode 0600, so read them via sudo.

### Emit a machine-readable event stream

Feed the trace into scripted analysis.

```
sudo procscope --jsonl events.jsonl -- /path/to/sample
```

Outcome: Every event lands in events.jsonl one per line; the traced command's own stdout is redirected to stderr so the JSONL stream stays parseable even when the command prints.

### Attach to a running process

Investigate something already running without restarting it.

```
sudo procscope --pid 1234
```

Outcome: Attaches to PID 1234 in place and traces it and its future children until interrupted — no relaunch, so runtime state is preserved.

### Attach by process name

Skip the PID lookup step.

```
sudo procscope --name suspicious
```

Outcome: Resolves the process named `suspicious` and attaches exactly as with --pid; combining --pid and --name is rejected before tracing starts.

*No corpus evidence — derived from upstream documentation.*
