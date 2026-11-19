---
name: "valhalla-api"
categories:
  - blackarch-automation
version: "87.c010a48"
homepage: "https://github.com/NextronSystems/valhallaAPI"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "NextronSystems/valhallaAPI"
github-stars: 77
github-pushed: "2026-08-24"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 38
gh-activity-checked: "2026-11-09"
---

# valhalla-api

> Python module and `valhalla-cli` client for the Nextron Valhalla threat-intel rule feed — pull filtered YARA and Sigma rules and do hash/rule lookups.

## What it's for

Valhalla is Nextron Systems' hosted repository of curated YARA and Sigma rules. This package provides both a Python module (`valhallaAPI.valhalla`) and a command-line client (`valhalla-cli`) to fetch your subscribed rule sets and filter them before they hit your scanners: by tag, minimum score, keyword, the YARA version your product supports, the YARA modules it compiles in, or a named product preset (FireEye, CarbonBlack, Tanium, Tenable, GRR, osquery). It also exposes lookup endpoints — given a SHA256 sample hash it returns the rules that match it, and given a rule name it returns metadata and matching sample hashes (rate-limited, customer features). A public demo API key (all-ones 64 chars) retrieves the free signature-base/SigmaHQ rule sets, so every command below is testable before you have a subscription. The client reads the key from `~/.valhalla` if you don't pass `-k`.

## Typical use cases

### Check feed status and subscription health

```
valhalla-cli -k YOUR-API-KEY --check
```

Outcome: prints a JSON account status (feed version, rule count, subscription state). Works with the demo key to verify connectivity before pulling rules.

### Pull the full YARA rule set

```
valhalla-cli -k YOUR-API-KEY -o valhalla-rules.yar
```

Outcome: writes every subscribed YARA rule (header comment with retrieval date and rule count) to `valhalla-rules.yar`, ready to deploy to your scanner.

### Fetch only high-confidence rules matching a keyword

```
valhalla-cli -k YOUR-API-KEY -fs 75 -fq Mimikatz -o mimikatz-rules.yar
```

Outcome: writes only rules scoring ≥ 75 (the "hard malicious" band) whose name or description mentions Mimikatz — a compact targeted set for hunting.

### Get a rule set compatible with your engine

```
valhalla-cli -k YOUR-API-KEY -fp CarbonBlack -o valhalla-cb.yar
```

Outcome: writes rules filtered to those that run on Carbon Black's supported YARA version and modules (equivalent presets exist for other products; `-fv 3.5.0 -fm pe` filters by explicit version/module instead).

### Retrieve the Sigma feed instead of YARA

```
valhalla-cli -k YOUR-API-KEY --sigma -o valhalla-sigma.zip
```

Outcome: writes the subscribed Sigma rules as a ZIP archive for import into your SIEM.

### Look up which rules match a sample hash

```
valhalla-cli -k YOUR-API-KEY -lh 8a883a74702f83a273e6c292c672f1144fd1cce8ee126cd90c95131e870744af
```

Outcome: queries the Valhalla database with a SHA256 hash and prints JSON listing every rule that matched that sample, with tags and timestamps — a quick triage of an unknown file.

### Pull rules from Python for pipeline use

```
python3 -c "from valhallaAPI.valhalla import ValhallaAPI; v=ValhallaAPI(api_key='YOUR-API-KEY'); open('rules.yar','w').write(v.get_rules_text(score=75))"
```

Outcome: uses the module directly to fetch score-75+ rules into `rules.yar`, for embedding rule sync into a scheduled script (the module also offers `get_rules_json`, `get_sigma_rules_zip`, `get_hash_info` and `get_rule_info`).

*No corpus evidence — derived from upstream documentation.*
