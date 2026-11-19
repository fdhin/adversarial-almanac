---
name: "git-wild-hunt"
categories:
  - blackarch-recon
version: "16.6495672"
homepage: "https://github.com/d1vious/git-wild-hunt"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "d1vious/git-wild-hunt"
github-stars: 297
github-pushed: "2022-12-08"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# git-wild-hunt

> Hunt credential files in the GitHub wild: run any advanced GitHub search, fetch the raw matches, and regex-check them against a truffleHog-style ruleset into results.json.

## What it's for

git-wild-hunt (d1vious / Jose Hernandez, Python 3) finds committed credentials at GitHub scale. Its single `-s` flag accepts any GitHub advanced-search query, so you aim it at the well-known leak locations — `.aws/credentials`, GCP/Azure JWT JSON files, `.kube/config`, Jenkins `credentials.xml`, CircleCI YAML and the rest — and it runs the search, pulls the raw content of matching files, and tests each against a bundled regex set (AWS, Google/GCP, Facebook, Slack, Stripe, Twilio, SSH and PGP private keys, generic secrets — borrowed from truffleHog's regexes and editable in `regexes.json`). Matches land in `results.json` (path configurable); a GitHub token goes in `git-wild-hunt.conf` alongside output, log and regex settings. It locates and extracts candidate credentials; it does not validate them against the live services.

## Typical use cases

### Find committed AWS credentials

Search the classic AWS CLI credential file.

```
git-wild-hunt -s "path:.aws/ filename:credentials"
```

Outcome: searches GitHub for files at `.aws/` named `credentials`, downloads each raw match, and appends every regex hit (AWS keys, access key IDs, MWS tokens) to `results.json` — review with `cat results.json | jq`.

### Hunt GCP and Azure token files

Go after JSON service-account and access tokens.

```
git-wild-hunt -s "extension:json filename:creds language:JSON"
```

Outcome: pulls JSON credential files (the same pattern works for Azure with `path:.azure filename:accessTokens`) and records Google service-account/API key and generic-secret matches; `.gsutil` credstore2 files respond to `path:.gsutil filename:credstore2`.

### Sweep for Kubernetes client configs

Find cluster admin kubeconfigs in public repos.

```
git-wild-hunt -s "path:.kube filename:config"
```

Outcome: retrieves matching `config` files under `.kube/` directories and writes any embedded credential matches to the configured JSON output — candidate cluster-access material for authorized verification.

### Run with an explicit config file

Point at a separate token/output configuration.

```
git-wild-hunt -c git-wild-hunt.conf -s "extension:xml filename:credentials.xml language:XML"
```

Outcome: loads the given conf (GitHub token, output path, regex file, logging) and runs the Jenkins `credentials.xml` hunt — pass `-c` whenever you keep separate configs per engagement instead of relying on the file in the working directory.

*No corpus evidence — derived from upstream documentation.*
