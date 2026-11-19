---
name: "fuddly"
categories:
  - blackarch-fuzzer
version: "0.30.dev.r414.g8ccabfd"
homepage: "https://github.com/k0retux/fuddly"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "k0retux/fuddly"
github-stars: 163
github-pushed: "2026-11-07"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 176
gh-activity-checked: "2026-11-09"
---

# fuddly

> Eric Lacombe's fuzzing and data-manipulation framework: describe formats as graph-based data models, generate and mutate them, send them to targets, monitor reactions through probes — with everything logged to an SQLite database.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Fuddly is a GNU/Linux framework covering the whole fuzzing workflow: structured formats are described as its own graph-based data models (which can also absorb and dissect existing data), generation and mutation strategies produce the actual fuzzed data, a target abstraction sends it (network services, local programs, serial links), independent probes monitor the target, and scenarios model protocol logic. Every sent data and reaction is recorded in the fmkDB SQLite database for replay and analysis. It ships data models for zip, png, pdf, jpg and USB frames, and is driven either from the interactive Fuddly Shell or directly from Python — it is a framework you write data models for, not a point-and-shoot fuzzer.

## Typical use cases

Shell commands below are typed at the Fuddly Shell `>>` prompt.

### Start a fuzzing session

Launch the shell and see what it discovers.

```
fuddly
```

Outcome: the Fuddly Shell starts, scans its data_models/ and projects/ directories and lists everything found (data models such as zip, png, pdf, jpg; projects such as standard), then presents the `>>` prompt; data, logs and the fmkDB database live under ~/.local/share/fuddly/.

### Wire up project, target and data model

Select what to fuzz before sending anything.

```
load_project standard
show_targets
load_targets 1
show_data_models
load_data_model zip
```

Outcome: the standard project is loaded, its configured targets are listed and target 1 selected, then the available data models are listed and the zip model loaded — the session is now wired end-to-end and ready to emit data.

### Send generated data to the target

Emit one data from the loaded model.

```
send ZIP
```

Outcome: one data generated from the ZIP model is sent to the loaded target, and both the data and any target feedback are logged into fmkDB; repeated sends walk the generator while everything stays individually recorded.

### Run a mutation loop

Send a stream of deterministically mutated data.

```
send_loop 500 ZIP(determinist=True) tWALK
```

Outcome: up to 500 ZIP data are sent, generated deterministically and walked through the data model by the generic stateful disruptor tWALK; use `-1` instead of a count to loop until the generator or disruptor chain is exhausted (stop early with Ctrl-C). Each iteration and any detected anomaly are recorded in fmkDB for review afterwards.
