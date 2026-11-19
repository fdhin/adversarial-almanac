---
name: "osmedeus"
categories:
  - recon
homepage: "https://www.osmedeus.org"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: go
github-repo: "j3ssie/Osmedeus"
github-stars: 6652
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
license: MIT
---

# osmedeus

> Declarative YAML orchestration engine that chains recon and attack tooling into auditable, repeatable workflows.

## What it's for

Osmedeus is a security-focused workflow engine by Jessie (@j3ssie): reconnaissance pipelines are described as readable YAML — single modules or multi-module "flows" — and the engine executes them on the local host, in Docker containers, or on remote machines over SSH, with a Redis-based master/worker mode for distributed parallel scanning. Built-in workflows chain subdomain enumeration, content discovery and screenshotting into one `osmedeus run` command; custom pipelines can branch on results (switch/case decision routing), fan out with `foreach`/`parallel-steps`, make raw HTTP requests, call LLM steps, and notify via Telegram bot or webhook. It orchestrates other tools rather than replacing them — the actual scanning work is done by whatever commands the workflow YAML invokes. Results land in per-target workspaces browsable through a web UI, and `osmedeus serve` exposes the same control surface as a REST API. It is designed to send lots of work at a single scope of targets, not to be a one-shot scanner you point at the internet.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Run a recon module against one domain

Kick off a single built-in workflow unit against a target.

```
osmedeus run -m recon -t example.com
```

Outcome: executes the `recon` module's steps against `example.com`, writing artifacts (outputs, logs, screenshots) under a per-target workspace directory; progress is visible in the CLI and, once enabled, the web UI.

### Run a full flow across many targets with concurrency

Orchestrate several modules per target, in parallel across a target list.

```
osmedeus run -f general -T targets.txt -c 5
```

Outcome: runs the `general` flow (a multi-module pipeline) against every domain in `targets.txt`, with 5 workflows executing concurrently; each target gets its own workspace with the merged flow results.

### Preview a run without executing anything

Check what a workflow would do before spending scan time.

```
osmedeus run -f general -t example.com --dry-run
```

Outcome: prints the resolved workflow — steps, commands with interpolated variables, and runners — without sending any traffic; use it to validate custom YAML or spot tools you do not want to fire.

### List available workflows

Discover the module and flow names the engine ships with.

```
osmedeus workflow list
```

Outcome: prints every installed module/flow workflow with its name and description — these names are what `-m`/`-f` accept. `osmedeus --usage-example` prints the full catalog of CLI invocations.

### Drive scans from the API

Expose workflow management to other tooling.

```
osmedeus serve
```

Outcome: starts the REST API server, letting you trigger workflows, query status and fetch results programmatically instead of via the CLI; the same instance supports cron, file-watch and webhook triggers for continuous monitoring.

### Write a custom module

Add your own pipeline as YAML.

```yaml
kind: module
name: demo-bash
description: Demo bash steps with functions and exports

params:
  - name: target
    required: true

steps:
  - name: setup
    type: bash
    command: mkdir -p {{Output}}/demo && echo "{{Target}}" > {{Output}}/demo/target.txt
    exports:
      target_file: "{{Output}}/demo/target.txt"

  - name: run-parallel
    type: bash
    parallel_commands:
      - 'echo "Thread 1: {{Target}}" >> {{Output}}/demo/results.txt'
      - 'echo "Thread 2: {{Target}}" >> {{Output}}/demo/results.txt'

  - name: check-result
    type: function
    function: 'fileLength("{{Output}}/demo/results.txt")'
    exports:
      line_count: "output"
```

Outcome: a runnable module (`osmedeus run -m demo-bash -t target.example.com`) showing the core step types — `bash`, `parallel_commands`, and `function` — plus `{{Output}}`/`{{Target}}` template variables and `exports` passing values between steps; richer variants add `foreach`, `http`, `llm` and `remote-bash` steps.
