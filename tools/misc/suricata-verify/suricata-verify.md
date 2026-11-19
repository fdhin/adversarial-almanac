---
name: "suricata-verify"
categories:
  - blackarch-misc
version: "suricata.8.0.7.r162.g06ccb56"
homepage: "https://github.com/OISF/suricata-verify"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "OISF/suricata-verify"
github-stars: 125
github-pushed: "2026-11-07"
github-archived: false
github-issues: 70
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 726
gh-activity-checked: "2026-11-09"
---

# suricata-verify

> The OISF regression suite for the Suricata engine: thousands of pcap-plus-expectation tests run through `run.py`.

## What it's for

suricata-verify is the Suricata IDS/IPS engine's own test corpus rather than a scanner: thousands of per-test directories, each holding a pcap, an optional per-test `suricata.yaml`, and a `test.yaml` describing the expected alerts and events. The `run.py` driver executes Suricata over each pcap and diffs the produced output against the expectations, which makes it the reference way to validate a freshly built or packaged Suricata, bisect an engine regression, or see concretely how a rule or protocol feature behaves on canned traffic. Replay tests additionally push pcaps through a real interface with tcpreplay. It needs a Suricata build to test and says nothing about your own network; `createst.py` scaffolds new tests from a pcap.

## Typical use cases

### Validate a Suricata build

Run the whole corpus against the engine you just compiled.

```
cd /path/to/suricata-source && /path/to/suricata-verify/run.py
```

Outcome: every test directory is executed with the local Suricata build; per-test PASS/FAIL results and a summary print as the run proceeds, and the process exits non-zero when tests fail.

### Run a single test by name

Re-check one failing case instead of the whole suite.

```
/path/to/suricata-verify/run.py no-payload-output
```

Outcome: only the named test directory runs — its pcap and expectations — reporting pass or fail in seconds.

### Exercise the IDS-on-the-wire path

Run the tests that replay traffic through a real interface instead of feeding pcaps directly.

```
/path/to/suricata-verify/run.py --replay
```

Outcome: pcaps from the `replay` test set are replayed with tcpreplay over a veth pair while Suricata listens in IDS mode (needs root); results report as with normal tests.

### Scaffold a new test from a pcap

Capture the evidence for an engine bug or a rule fix as a permanent test.

```
/path/to/suricata-verify/createst.py --strictcsums --eventtype-only my-test input.pcap
```

Outcome: a `tests/my-test/` directory is created containing the pcap and a generated `test.yaml` of expected events — review, adjust, and commit it upstream.

*No corpus evidence — derived from upstream documentation.*
