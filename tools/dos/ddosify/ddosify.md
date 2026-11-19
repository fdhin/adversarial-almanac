---
name: "ddosify"
categories:
  - blackarch-dos
version: "v2.6.0.r32.g5cf7df3"
homepage: "https://github.com/getanteon/anteon"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-03-07"
build-system: go
license: AGPL
meta-checked: "2026-11-09"
github-repo: "getanteon/anteon"
github-stars: 8513
github-pushed: "2026-11-09"
github-archived: false
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 52
gh-activity-checked: "2026-11-09"
---

# ddosify

> The Anteon (formerly Ddosify) load-testing engine — single-command HTTP benchmarking, JSON config scenarios and CI-friendly output in one Go binary.

## What it's for

Ddosify is the command-line load engine behind the Anteon performance-testing platform — a k6/Gatling-alike packaged as a single Go binary. One command benchmarks a URL immediately (iterations, duration, method, timeout, proxy, auth and headers as flags), while JSON config files describe multi-step scenarios with captured variables, parameterization, test data and success criteria. It reports latency distributions, throughput and error breakdowns to the terminal, as NDJSON for pipelines, or as an HTML report via `ddosify report`. The v1 flag-only invocation (`ddosify -t URL`) still works. It is a load-testing tool for systems you own, not an attack tool — the same engine also drives LLM/streaming endpoint tests.

### Benchmark a URL with defaults

The quickest possible test: a URL and nothing else.

```
ddosify run http://target.example.com/
```

Outcome: runs 100 iterations over 10 seconds against the URL and prints a result table on stdout — success/failure counts, latency percentiles, average duration and throughput; the exit code is 0 when the run passed, 3 when every iteration failed.

### Size and shape a test with flags

Set volume, duration, method, timeout and an outbound proxy explicitly.

```
ddosify run http://target.example.com/ -n 1000 -d 20 -m PUT -T 7 -P http://proxy.example.com:8080
```

Outcome: 1,000 PUT requests spread over 20 seconds, each with a 7-second timeout, all issued through the given proxy; the final report reflects the proxy's added latency alongside the target's own.

### Machine-readable output for CI pipelines

Emit the report as JSON and slice it downstream.

```
ddosify run http://target.example.com/ -o stdout-json | jq .avg_duration
```

Outcome: prints the run report as a single JSON object instead of a formatted table, so pipelines can assert on fields like average duration; exit codes 2 (bad invocation/config) and 3 (failed run) gate the pipeline stage. `--html report.html` additionally writes a shareable HTML report when the build includes it.

### Run a multi-step scenario from a config file

Anything a single URL cannot express: logins, sequences, assertions.

```
ddosify run --config config.json
```

Outcome: executes the scenario described by the config file (multiple steps, auth, captured variables, success criteria) and prints per-step results; `ddosify report run.ndjson` afterwards turns a run's NDJSON log into an HTML report page.

*No corpus evidence — derived from upstream documentation.*
