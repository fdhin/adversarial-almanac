---
name: "gron"
categories:
  - misc
version: "git (2025-05-31)"
homepage: "https://github.com/tomnomnom/gron"
origin: "github"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: go
meta-checked: "2026-11-09"
github-repo: "tomnomnom/gron"
github-stars: 14538
github-pushed: "2025-05-31"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
arch-builddate: "2026-06-03"
github-issues: 57
---

# gron

> Make JSON greppable by turning it into discrete assignments — and turn them back into JSON.

## What it's for

gron flattens JSON into one assignment per line (`json.contact.email = "..."`), so `grep` can find values and show the absolute path to them at the same time. It exists for exploring large, poorly documented API responses where you know a value exists but not where it nests. The inverse (`--ungron`) converts filtered assignments back into valid JSON, so grep-based filtering becomes a JSON query pipeline. It reads files, URLs, and stdin, and has no runtime dependencies. It is read-only transformation — it never modifies the source JSON.

## Typical use cases

### Flatten a JSON file for grepping

Turn a nested document into one assignment per line.

```
gron data.json
```

Outcome: prints every leaf as `json.path.to.key = value;`, sorted, making the document's full structure visible at a glance.

### Grep a value out of an API response and see its path

Fetch a JSON endpoint, locate a key anywhere in the tree, and learn where it lives.

```
gron https://api.example.com/v1/commits | fgrep "author.email"
```

Outcome: prints only the assignment lines containing the phrase, each showing the absolute path to the matching value.

### Grep JSON, then rebuild it

Filter assignments with grep and convert the survivors back into a JSON document.

```
gron https://api.example.com/v1/commits | fgrep "author" | gron --ungron
```

Outcome: emits valid JSON containing only the objects whose assignments survived the filter — a grep-based subselect of the original response.

### Flatten JSON from stdin

Use gron in the middle of a shell pipeline without a temp file.

```
curl -s https://api.example.com/status | gron
```

Outcome: prints the flattened assignments of the response body to stdout for further filtering.

### Diff two JSON documents structurally

Compare two versions of a document field-by-field regardless of key ordering.

```
diff <(gron old.json) <(gron new.json)
```

Outcome: shows exactly which assignment lines differ, highlighting changed, added, and removed values with their full paths.

*No corpus evidence — derived from upstream documentation.*
