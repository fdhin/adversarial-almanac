---
name: "malybuzz"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "http://eternal-todo.com/tools/malybuzz-network-fuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "Seabreg/Malybuzz"
github-stars: 0
github-pushed: "2019-09-14"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# malybuzz

> Python network protocol fuzzer that drives a TCP/UDP service with fuzzed command sequences built from XML protocol models, with session save and resume.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Malybuzz (Jose Miguel Esparza, S21sec, 2007) sends fuzzed protocol command sequences at a listening service to find programming faults — parsing errors, overflows, unhandled states. Protocol behavior is described in XML command/response specification files (FTP and SIP models ship built in), with control over banner handling, timeouts, request pacing, and logging. Interrupted runs save their state and can be resumed, so long campaigns survive restarts. It is model-driven rather than a raw byte mutator: the command grammar of the protocol defines what gets fuzzed.

## Typical use cases

### Fuzz an FTP service

Run the built-in FTP model against a listening server.

```
malybuzz 198.51.100.10 tcp ftp -p 21 -b -l 2
```

Outcome: connects to the FTP port, waits for the banner (`-b`), and replays fuzzed command sequences while logging at level 2 (info) under `Logs/`; response timeout and pacing follow the defaults (100000 ms response timeout, 500 ms between requests).

### Fuzz SIP over UDP with session checkpointing

Attack a stateless transport while keeping the run resumable.

```
malybuzz 198.51.100.10 udp sip -p 5060 -k
```

Outcome: fuzzes the SIP model over UDP; with `-k` the fuzzing state is checkpointed into the `Restore/` folder as it progresses, so an aborted run can be continued instead of restarted.

### Generate the command list before fuzzing

Preview exactly which commands a run would send.

```
malybuzz 198.51.100.10 tcp ftp -g commands.xml cmds_ftp
```

Outcome: instead of fuzzing, extracts the command sequence defined by `commands.xml` and stores it under `Restore/`, letting you review or curate the commands before a real campaign.

### Resume a saved fuzzing session

Continue a campaign from its checkpoint.

```
malybuzz -z Restore/fuzzing-session
```

Outcome: reloads the session file saved by an earlier `-k` run and continues the fuzzing from where it stopped, preserving the commands already sent.
