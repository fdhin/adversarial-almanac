---
name: "jc"
categories:
  - misc
version: "git (2026-11-07)"
homepage: "https://github.com/kellyjonbrazil/jc"
origin: "github"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: python
meta-checked: "2026-11-09"
github-repo: "kellyjonbrazil/jc"
github-stars: 8703
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 699
gh-activity-checked: "2026-11-09"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
arch-builddate: "2026-11-03"
github-issues: 46
---

# jc

> Converts the output of popular CLI commands, file types, and common strings into JSON (or YAML) so it can be piped into jq and scripted.

## What it's for

jc parses the human-oriented output of hundreds of commands and file formats — `ping`, `ps`, `arp`, `df`, `traceroute`, CSV/INI files, `/etc/hosts`, `/etc/passwd`, log formats — into structured JSON. A command is wrapped either by piping (`ps | jc --ps`) or with the "magic" syntax that prepends `jc` to the command (`jc ps`); shell builtins and aliases are not supported in magic syntax. The same parsers are available as a Python library. Use it whenever you need to script against a tool that only prints text, or to turn ad-hoc command output into data a downstream tool can consume.

## Typical use cases

### Wrap a command and pretty-print

```
jc -p ping -c 3 198.51.100.10
```

Outcome: runs ping, then prints its parsed JSON — destination_ip, packets_transmitted, packets_received, packet_loss_percent, round_trip_ms stats — nicely formatted (`-p` pretty-prints; the default output is compact single-line JSON).

### Parse piped output for jq

```
ps axu | jc --ps | jq -r '.[] | select(.user=="root") | .pid'
```

Outcome: the process table becomes a JSON array of objects (user, pid, cpu_percent, mem_percent, ...) that jq can filter — the core pattern for scripting against text-only tools.

### Parse a file

```
cat data.csv | jc --csv
```

Outcome: prints the CSV as a JSON array of row objects keyed by the header row; `--yaml-out` switches the output format to YAML, and `-r` gives raw (less semantically processed) values.

### Look up a parser's options

```
jc -h --ping
```

Outcome: prints the documentation for that single parser — the exact flags it expects and the output fields it produces — instead of the general help.

*No corpus evidence — derived from upstream documentation.*
