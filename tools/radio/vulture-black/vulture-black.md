---
name: "vulture-black"
categories:
  - blackarch-radio
version: "147.8b7a1ce"
homepage: "https://github.com/black-210/VULTURE"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: python
license: AGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "black-210/VULTURE"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 2
github-pushed: "2026-10-09"
github-archived: false
github-issues: 0
github-commits-year: 147
gh-activity-checked: "2026-11-09"
---

# vulture-black

> VULTURE — offline, receive-only RF analysis and forensic-audit CLI: RF-DNA fingerprinting, IQ processing, physics/chemistry calculators.

*No corpus evidence — derived from upstream documentation.*

## What it's for

VULTURE is a command-line and Python-API toolkit for authorized, local, offline analysis of RF captures — it deliberately transmits nothing, opens no hardware by default, and reports its disabled network/hardware state. Its scope is unusually wide: canonical `.iq`/`.npz` capture handling with provenance manifests, RF-DNA device fingerprinting (feature extraction, digests, similarity metrics, provenance dashboards), ML training/classification hooks, and a "forensic audit" layer that records physics/chemistry/mathematics analyses with case IDs as evidence chains. The vault name `vulture-black` distinguishes it from the unrelated Python dead-code checker `vulture`; the upstream repository is `black-210/VULTURE`.

## Typical use cases

### Confirm the safe offline runtime

```
vulture status
```

Outcome: prints a JSON status block — `mode: offline-deterministic`, `hardware: not-opened`, `network: disabled`, `rf_transmit: disabled` — verifying the session is local-only before analysis (`vulture info` additionally lists the available science and forensic modules).

### Generate a synthetic test capture

```
vulture rf-dna simulate --profile multi-tone --duration 2 --sample-rate 1000000 --seed 7 --output capture.npz
```

Outcome: writes a deterministic synthetic IQ capture (`cw`, `fsk`, `chirp`, `noise` and other profiles available) to `capture.npz`, giving the pipeline reproducible input when no hardware capture exists.

### Fingerprint a capture

```
vulture rf-dna fingerprint --input capture.npz --label lab-device-01
```

Outcome: extracts the RF-DNA features of the capture and prints a device fingerprint with digest and similarity metrics; `vulture rf-dna report --input capture.npz --label report-001` emits the full machine-readable analysis with timestamps and evidence chain.

### Run physics calculations with provenance

```
vulture chemical-rf physics --input capture.npz --frequency-hz 2.4e9 --distance-m 10
```

Outcome: computes wavelength and free-space path loss for the given parameters, linked to the capture's metadata; the `forensic` subcommands (e.g. `vulture forensic physics --case-id C-001 ...`) produce the same analyses as auditable, case-tagged evidence records.
