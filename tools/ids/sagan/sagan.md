---
name: "sagan"
categories:
  - blackarch-ids
version: "2.0.2"
homepage: "https://quadrantsec.com/sagan_log_analysis_engine/"
packaged-by: [blackarch]
blackarch-builddate: "2023-03-30"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "quadrantsec/sagan"
github-stars: 200
github-pushed: "2025-10-06"
github-archived: false
github-issues: 50
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sagan

> Real-time, multi-threaded log analysis and correlation engine that applies Snort/Suricata-style rules to syslog events instead of network packets.

## What it's for

Sagan (Champ Clark III / Quadrant Information Security, GPLv2) is an
open-source, high-performance log analysis engine written in C. Its
structure and rule syntax deliberately mirror Snort and Suricata so that
existing rule-management tooling (oinkmaster, pulledpork) works and log
events can be correlated with IDS/IPS alerts. Sagan reads syslog input
(normally from a FIFO fed by syslog-ng or rsyslog), normalizes it via
liblognorm, and matches it against rules that support the familiar
`alert ... (content: ...; ...)` syntax plus log-specific options
(`parse_src_ip`, `parse_hash`, `flexbit`/`xbits` cross-event state,
thresholds, GeoIP, threat-intel and blacklist lookups). Output goes to
plain logs, syslog, Snort unified2, or Suricata-style EVE JSON for
Elasticsearch/EveBox-style consoles, and alerts can trigger scripts or
firewall actions via Snortsam. Rules ship separately in the
quadrantsec/sagan-rules repository. The project's last upstream push was
2023/2025-era; the packaged 2.0.2 is the stable line.

## Typical use cases

### Test the configuration and rule sets before starting

```
sagan -T
```

Outcome: Sagan parses `sagan.yaml` (found via the compiled-in default or
`-f`) and every rule file it references, then exits reporting either
that the configuration and rules are valid or the exact file, line and
rule option that failed to load — catching syntax errors before the
daemon goes live.

### Run the engine as a daemon on the log FIFO

```
sagan -D -u sagan -f /etc/sagan/sagan.yaml -r /etc/sagan/rules/*.rules
```

Outcome: Sagan forks to the background, drops privileges to the `sagan`
user, loads the named configuration and rule files (overriding the rule
paths from `sagan.yaml`), and begins consuming normalized syslog events
from its input FIFO, inspecting them with all CPU threads and writing
alerts per the configured outputs. Errors and lifecycle messages go to
the `sagan.log` set by `-l` (default compiled-in path).

### Replay a captured log file for rule testing

```
sagan -F /tmp/captured-syslog.log -t 4
```

Outcome: instead of blocking on the live FIFO, Sagan reads the file
(must already be in Sagan's expected log format) at full speed with 4
processor threads, letting you iterate on rule tuning against known
input and watch which lines fire which alerts without touching
production logging.

### Debug why rules are (or are not) firing

```
sagan -d engine -F /tmp/captured-syslog.log
```

Outcome: Sagan runs in the foreground emitting verbose engine-debug
output for every event — rule evaluation order, normalization results
and parse failures — which is the standard way to find why a
`parse_src_ip` or content match behaves differently than expected.
Other `-d` types cover `syslog`, `confload`, `normalize`, `xbit` and
more (see `-h` for the compile-dependent list).

*No corpus evidence — derived from upstream documentation.*
