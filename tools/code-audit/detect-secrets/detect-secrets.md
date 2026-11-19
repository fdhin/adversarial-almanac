---
name: "detect-secrets"
categories:
  - blackarch-code-audit
version: "v1.5.0.r50.g5e141933"
homepage: "https://github.com/Yelp/detect-secrets"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-08-02"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "Yelp/detect-secrets"
github-stars: 4666
github-pushed: "2026-04-02"
github-archived: false
github-issues: 195
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# detect-secrets

> Enterprise-friendly secret detection for code bases: snapshot existing secrets into a baseline, then block new ones at commit time — with plugin-based detection, filter tuning and an audit workflow.

*No corpus evidence — derived from upstream documentation.*

## What it's for

detect-secrets scans code for credentials — AWS/GitHub/Stripe/Slack-style API keys, private keys, JWTs, basic-auth URLs, plus high-entropy and keyword heuristics across ~25 plugin families. Its distinguishing idea is the baseline workflow for large existing repositories: accept that secrets are already present, record them in a baseline file, and from then on only *new* secrets block a commit, so adoption doesn't require first rotating every legacy secret. Detection runs through plugins (regex rules, entropy detectors, keyword detector) and results pass through filters that remove known false positives; inline `# pragma: allowlist secret` comments exempt individual lines. It is prevention, not remediation — the audit step yields the checklist of real secrets to rotate and move to a proper secret store. Three entry points: `detect-secrets scan` builds the baseline, `detect-secrets-hook` gates commits against it, `detect-secrets audit` triages it.

## Typical use cases

### Snapshot existing secrets into a baseline

```
detect-secrets scan > .secrets.baseline
```

Outcome: scans all git-tracked files and writes a JSON baseline recording every currently detected secret together with the plugins and filters used, so those findings count as known from now on. Add `--all-files` to include untracked files, or `-C /path/to/dir` to scan a different tree.

### Refresh the baseline after code changes

```
detect-secrets scan --baseline .secrets.baseline
```

Outcome: rescans the codebase and rewrites the baseline in place — upgrading it to the current tool version, adding newly found secrets, dropping entries whose code disappeared, and preserving audit labels. Commit the updated baseline together with the change that introduced the new secret.

### Block new secrets at commit time

```
git diff --staged --name-only -z | xargs -0 detect-secrets-hook --baseline .secrets.baseline
```

Outcome: scans only the staged files and exits non-zero, printing each offending file and secret, when a secret appears that is not in the baseline — wire it into a pre-commit hook. `git ls-files -z | xargs -0 detect-secrets-hook --baseline .secrets.baseline` does the same over all tracked files.

### Triage and label baseline findings

```
detect-secrets audit .secrets.baseline
```

Outcome: opens an interactive session to label each baseline entry as a real or false positive; labels are stored in the baseline, and `--stats`/`--report` later summarize how many real secrets still need rotating versus noise.

### Cut noise by disabling plugins and skipping files

```
detect-secrets scan --disable-plugin KeywordDetector --exclude-files '.*test.*' > .secrets.baseline
```

Outcome: builds a baseline without the keyword-detector plugin (a common false-positive source) and without any file matching the exclude regex; `detect-secrets scan --list-all-plugins` enumerates the plugin classes available to disable, and `--exclude-lines`/`--exclude-secrets` filter at the line and value level the same way.
