---
name: "bandicoot"
categories:
  - blackarch-mobile
version: "0.6.0"
homepage: "https://pypi.org/project/bandicoot/#files"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "computationalprivacy/bandicoot"
github-stars: 241
github-pushed: "2021-01-04"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bandicoot

> Compute reproducible behavioral indicators — weekly reports, mobility and network measures — from mobile phone metadata.

## What it's for

bandicoot is a dependency-free Python library from Yves-Alexandre de Montjoye's group (computationalprivacy / MIT) for research on mobile phone metadata: call detail records with optional antenna locations. It loads a user's records (calls, texts, recharges, location traces), computes a standard set of individual and spatial indicators — interevent time, number of contacts, entropy of contacts, response rate and delay, percent nocturnal, active days, radius of gyration and other mobility measures — and exports the results as JSON/CSV or an interactive HTML dashboard. It is a library, not a CLI: there is no `bandicoot` command and no console entry point. Version 0.6.0 dates from Python's 2/3 era and fails to import on Python 3.11+ (`inspect.getargspec` was removed), so run it under Python 3.10 or older.

## Typical use cases

### Load a user's records and compute every indicator

```python
import bandicoot as bc

user = bc.read_csv('sample_records', '.', describe=True)
indicators = bc.utils.all(user)
```

Outcome: prints a summary of the loaded user (records, contacts, date range) and returns a dict of all individual/spatial indicators grouped by week, plus reporting variables such as `has_home` and `percent_records_missing_location`.

### Load antenna coordinates alongside the records

```python
user = bc.read_csv('sample_records', '.', 'sample_places.csv')
```

Outcome: each record's `place_id` is mapped to latitude/longitude from the CSV, enabling the spatial indicators (home location, radius of gyration).

### Export computed indicators for downstream analysis

```python
bc.io.to_json([indicators], 'indicators.json')
```

Outcome: writes the indicator dictionaries of one or several users to a JSON file suitable for pandas or scikit-learn pipelines.

### Explore a user in the interactive dashboard

```python
bc.visualization.run(user, port=4242)
```

Outcome: serves the bandicoot web dashboard at `http://localhost:4242` with maps, network graphs and indicator charts for the loaded user; `bc.visualization.export(user, 'dashboard/')` writes the same dashboard as static files instead.

*No corpus evidence — derived from upstream documentation.*
