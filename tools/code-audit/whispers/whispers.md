---
name: "whispers"
categories:
  - blackarch-code-audit
version: "2.4.0.r0.g24ee0f0"
homepage: "https://github.com/adeptex/whispers/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-14"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "adeptex/whispers"
github-stars: 100
github-pushed: "2025-02-05"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# whispers

> Hardcoded-secret detector for structured text (YAML, JSON, XML, properties, ...) and static code — pulls key/value pairs out of configs and source and matches them against tunable rule groups (CWE-798).

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding credentials baked into configuration files and code: CI configs, Helm charts, compose files, `.npmrc`/`.pypirc`, cloud deployment manifests. Files are parsed into key/value pairs and checked against rules grouped as keys (AWS secrets, private keys, API keys), passwords, infra (dockercfg, htpasswd, pip/npm configs) and misc (webhooks, credit cards). With `-a` the same rules run over source code through AST parsing (semgrep engine) covering Python, PHP, Java/Scala/Kotlin, JS/TS, Go and more. Everything — which files, keys, values, rules and severities to include or exclude — is controllable via CLI flags or a YAML config, which is what makes it pipeline-friendly. It reports matches only; it does not verify whether a found credential is live, so triage hits before paging anyone.

## Typical use cases

### Check a directory of config files

```
whispers target/dir
```

Outcome: every supported structured-text file under the target is parsed and checked; each finding is logged with rule, severity, file:line and the offending key/value pair, and the full result set is dumped as a JSON array (to stdout unless `-o` redirects it).

### Include source code via AST analysis

```
whispers -a target/dir
```

Outcome: in addition to structured text, source files are AST-parsed so hardcoded assignments inside code (not just config files) are checked against the same rules.

### Write JSON results to a file

```
whispers target/dir -o /tmp/secrets.json
```

Outcome: findings are collected into a single JSON array in the given file instead of the screen, ready for downstream tooling (`jq '.[].value'` to pull just the secret values).

### Filter by rule group, severity and excluded files

```
whispers -g keys -s Critical,High -F '.*\.(log|raw)' target/dir
```

Outcome: only the `keys` rule group is reported, only at Critical/High severity, with `.log` and `.raw` files excluded by regex. Every dimension has an inverse flag for fine control: `-G/--xgroups`, `-r/--rules`/`-R/--xrules`, `-s/--severity`/`-S/--xseverity`, `-f/--files`/`-F/--xfiles`.

### Run from a customized config file

```
whispers --init > config.yml
```

```
whispers --config config.yml target/dir
```

Outcome: `--init` prints the default config (include/exclude lists plus inline custom rule definitions) to seed `config.yml`; after editing it, `--config` applies those filters and any custom rules — the repeatable way to silence known false positives across runs.
