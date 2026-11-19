---
name: "interlace"
categories:
  - blackarch-networking
version: "356.b9eb99f"
homepage: "https://github.com/codingo/Interlace/releases"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "codingo/Interlace"
github-stars: 1306
github-pushed: "2025-09-12"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# interlace

> Wraps any single-threaded command-line tool in a thread pool, expanding CIDR, glob and dash target notation, port ranges, exclusions and proxy lists into one fan-out command.

## What it's for

Interlace (by codingo and sml555) turns "run this command against every host" into a single multi-threaded invocation for tools that lack built-in parallelism or target-notation support. Give it targets (`-t`/`-tL`, or piped on stdin), optional ports (`-p`), proxies (`-pL`) and exclusions (`-e`/`-eL`), plus a command (`-c`) or command file (`-cL`) containing `_target_`, `_port_`, `_proxy_` and `_output_` placeholders; it expands CIDR (`198.51.100.0/24`), glob (`198.51.100.*`) and dash (`198.51.100.1-15`) notation, then runs the command per target per port across up to `-threads` workers. Command files also support `_blocker_` and `_block:<name>_` directives to force execution order per target. It is a launcher/parallelizer, not a scanner — the wrapped tool produces the actual output.

## Typical use cases

### Fan a command out over a target list

Run one scanner over many hosts concurrently.

```
interlace -tL targets.txt -threads 5 -c "nikto -host _target_ > _target_-nikto.txt" -v
```

Outcome: queues one nikto run per line of `targets.txt` across 5 threads; each host's output lands in its own `<host>-nikto.txt`, and `-v` shows each queued command in the terminal.

### Iterate ports as well as targets

Multiply the command over a port set.

```
interlace -t example.com -p 80,443 -c "sslscan _target_:_port_ > _output_/_target_-_port_-sslscan.txt" -o ~/scans/
```

Outcome: runs sslscan against `example.com` on port 80 and on port 443 as separate threads, writing one file per target-port pair into `~/scans/`; `-p` also accepts dash ranges like `1-80`.

### Give CIDR support to a tool that lacks it

Expand a network range into individual hosts, minus exclusions.

```
interlace -t 198.51.100.0/24 -e 198.51.100.0/26 -c "vhostscan _target_ -oN _output_/_target_-vhosts.txt" -o ~/scans/ -threads 50
```

Outcome: expands the /24 to individual hosts (skipping the excluded /26) and runs vhostscan against each across up to 50 threads — even though vhostscan itself understands no CIDR, glob or dash notation.

### Run a standard command checklist per engagement

Reuse a saved list of commands with ordering control.

```
interlace -t example.com -o ~/engagements/example/ -cL commands.txt -p 80,443
```

Outcome: executes every line of `commands.txt` (each using `_target_`, `_port_`, `_output_`) for both ports, storing results under the engagement folder; a `_blocker_` line in the file holds later commands until earlier ones finish for that target, and `_block:<name>_` ... `_block:<name>_` pairs run their contents sequentially.

*No corpus evidence — derived from upstream documentation.*
