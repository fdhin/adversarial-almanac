---
name: "lightbulb"
categories:
  - blackarch-webapp
version: "88.9e8d6f3"
homepage: "https://github.com/lightbulb-framework/lightbulb-framework"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "lightbulb-framework/lightbulb-framework"
github-stars: 465
github-pushed: "2020-11-24"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lightbulb

> Python framework for auditing web application firewalls and input filters with automata-learning algorithms.

## What it's for

LightBulb models web application firewalls (WAFs), filters, and sanitizers as black boxes: it queries the target with crafted inputs, observes what passes, and learns a symbolic automaton of its behavior. It combines two algorithms — GOFA (active learning of automata models via membership/equivalence queries) and SFADiff (black-box differential testing with Symbolic Finite Automata) — to fingerprint filters or generate inputs that evade them. Reach for it when you need evidence that a WAF can be bypassed for a given vulnerability class (the release bundles XSS/SQLi grammars used to find real bypasses in ModSecurity, PHPIDS, and Expose). It is not a general web vulnerability scanner, and the standalone console requires a MySQL server and OpenFst for some modules; a Burp Suite extension variant exists with a GUI.

## Typical use cases

### Check and install runtime dependencies

The console needs optional native bindings before first use; the built-in checker installs or reports them.

```
lightbulb status
```

Outcome: prints a dependency checklist (flex, MySQL bindings, OpenFst) and installs what is missing; run this once after installation before starting a learning run.

### Browse modules from the interactive console

LightBulb is driven from an interactive shell started with no arguments.

```
lightbulb
```

Then inside the console: `modules` lists available application modules, `info <module>` prints what a module does, and `library` enters the bundled grammar library where `search xss,sql` finds library grammars by keyword.

Outcome: an interactive prompt where application modules (attack filters to model) and library modules (grammars/data) can be inspected before use.

### Configure a module and start a learning run

Inside the console, enter a module, point it at the target, and start the algorithm.

```
lightbulb
```

Then: `use <module>`, `options` to list the module's settings, `define <option> <value>` to set the target URL and grammar, and `start` to begin learning. From the main prompt, `start <moduleA> <moduleB>` runs a differential (SFADiff) comparison of two modules directly.

Outcome: the learner queries the target, prints progress, and finally outputs a learned model of the filter plus any evasion inputs it found.

No corpus evidence — derived from upstream documentation.
