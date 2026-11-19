---
name: "sdnpwn"
categories:
  - blackarch-scanner
version: "85.0080574"
homepage: "https://github.com/smythtech/sdnpwn"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "smythtech/sdnpwn"
github-stars: 115
github-pushed: "2024-10-04"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sdnpwn

> Module-based Python toolkit for penetration-testing Software-Defined Networks (SDN).

## What it's for

sdnpwn (smythtech) collects attacks against SDN infrastructure into one toolkit: controller fingerprinting, fake-switch interaction with OpenFlow controllers, and controller-application testing each live in their own module with their own options, and the framework dispatches `<module> <options>` invocations. The project's article/tutorial site (sdnpwn.net) documented per-module walkthroughs but is currently unreachable, and the README's own TODO list flags stale or broken modules (the OpenFlow library, setup script, detection accuracy) — verify each module in a lab before relying on its output, and use it only against infrastructure you are authorized to test.

## Typical use cases

### List available modules

See what attacks the toolkit ships.

```
sdnpwn mods
```

Outcome: the `mods` module prints every module's name; the packaged `sdnpwn` wrapper runs `sdnpwn.py` (upstream form: `./sdnpwn.py mods`).

### Inspect a module's options

Read a module's description and parameters before running it.

```
sdnpwn info mods
```

Outcome: the `info` module prints the named module's description and available options (here for `mods` itself; `info <module>` works for any other).

### Run an attack module

Execute a module against authorized test infrastructure.

```
sdnpwn <module> <options>
```

Outcome: dispatches to the named module with its options — for example the SDN detection/fingerprinting or fake-switch modules against a controller at an authorized test address; the exact options each module accepts are listed by `info <module>`.

*No corpus evidence — derived from upstream documentation (smythtech/sdnpwn README; packaged entry point from the BlackArch recipe).*
