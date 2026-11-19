---
name: "ultrafuzz"
categories:
  - fuzzer
version: "git (2026-11-04)"
homepage: "https://github.com/monad-developers/ultrafuzz"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: node
meta-checked: "2026-11-09"
github-repo: "monad-developers/ultrafuzz"
github-stars: 142
github-pushed: "2026-11-04"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 655
gh-activity-checked: "2026-11-09"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 34
---

# ultrafuzz

> Agentic orchestrator that drives AI coding agents to fuzz and threat-hunt smart-contract codebases.

## What it's for

Ultrafuzz (Monad Foundation) coordinates specialized AI agents over a protocol repository: it scaffolds the project with editable prompts and topology, launches fuzz-test generation and threat-hunting campaigns, collects the generated tests and findings, and serves a local dashboard plus a final report for review. Reach for it when you want an agent workflow to produce runnable fuzz tests and a findings report for a smart-contract codebase instead of hand-writing the harness. It is not itself a fuzzer — the agents write and run the tests — and because those agents run in an unrestricted, skip-permissions workflow, it must only be used on ephemeral, isolated virtual machines that can be discarded afterwards.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Scaffold a campaign project

Initialize the editable prompts, topology, pinned references and workflow plumbing for a target repository.

```
ultrafuzz init --project /path/to/protocol
```

Outcome: writes `ultrafuzz.toml` and the `.ultrafuzz/` run surfaces (prompts, pinned reference catalog, workflow plumbing) inside the project; the result summary lists created, preserved and overwritten paths. Review the checked-in prompts before launching anything.

### Validate configuration before launching

Check config, topology, prompts, safe paths, trust posture and agent references without running a campaign.

```
ultrafuzz validate --project /path/to/protocol
```

Outcome: exits cleanly with an empty diagnostics list when the project is launch-ready; every violation is reported as a diagnostic to fix first.

### Launch and monitor a fuzzing campaign

Start the agent workflow and watch it until it terminates.

```
ultrafuzz run --project /path/to/protocol --run-id campaign-1
ultrafuzz status campaign-1 --watch
```

Outcome: `run` plans the workflow, renders prompts, launches the fuzzing nodes and persists run evidence; `status --watch` streams the health verdict, node progress, ETA and gating nodes until the run is terminal. `why <run-id>` explains any blocker.

### Review results and serve the dashboard

Inspect the agent-written report and browse product evidence locally.

```
ultrafuzz dashboard
ultrafuzz report campaign-1
```

Outcome: `dashboard` serves the loopback dashboard and API over product state; `report` prints the final agent-written report artifact for the run, and reviewed outputs can afterwards be copied into the project with `ultrafuzz materialize`.
