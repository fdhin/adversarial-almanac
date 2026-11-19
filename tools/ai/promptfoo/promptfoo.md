---
name: "promptfoo"
categories:
  - blackarch-ai
version: "0.120.20"
homepage: "https://github.com/promptfoo/promptfoo"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-02-12"
build-system: node
license: MIT
meta-checked: "2026-11-09"
github-repo: "promptfoo/promptfoo"
github-stars: 26933
github-pushed: "2026-11-07"
github-archived: false
github-issues: 929
github-match: "exact"
gh-checked: "2026-11-07"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4562
gh-activity-checked: "2026-11-09"
---

# promptfoo

> Test and evaluate LLM outputs — assertion-based test suites and red-teaming for prompts, models, RAG pipelines and agents, from the command line.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Making LLM behavior reproducible. You describe tests in config: prompts and providers under test, test cases with expected behaviors, and assertions (contains, equals, model-graded checks, JavaScript predicates). `promptfoo eval` runs the matrix — every prompt variant against every provider — and reports a table of pass/fail per assertion, so regressions from a prompt edit or model swap become visible instead of vibes. The same engine powers red-teaming: probe an LLM app for injection, jailbreaks and data leaks with built-in attack strategies. It lives in CI as naturally as a unit test suite.

## Typical use cases

### Scaffold a first evaluation project

```
promptfoo init --example getting-started
```

Outcome: a working `promptfooconfig.yaml` with providers, prompts and test cases appears in the current directory — edit it to point at your model endpoint and assertions, then evaluate.

### Run the suite and review results

```
promptfoo eval
promptfoo view
```

Outcome: every test case is executed against every prompt/provider combination, with each assertion graded pass/fail; `view` opens the results table in a browser (or the web viewer) for side-by-side comparison of outputs and failures — the artifact you bring to a model-choice or prompt-change decision.
