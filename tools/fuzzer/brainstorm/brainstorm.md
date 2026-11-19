---
name: "brainstorm"
categories:
  - blackarch-fuzzer
version: "38.c5b66c6"
homepage: "https://github.com/s7x/brainstorm"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-07"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "s7x/brainstorm"
github-stars: 1
github-pushed: "2026-03-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# brainstorm

> Web path and filename discovery that couples ffuf with a local Ollama LLM — the model proposes candidate paths, ffuf verifies them, and findings feed the next round.

## What it's for

brainstorm (originally by harisec at Invicti Security; the packaged s7x fork adds conveniences such as a custom output directory) wraps an ordinary ffuf command in a learning loop: extract the target site's links, have a locally running Ollama model (default qwen3:4b-instruct) analyze the structure and suggest plausible new paths and filenames, fuzz those suggestions with your ffuf invocation, add confirmed discoveries to the corpus and repeat for a fixed number of cycles. It finds hidden endpoints, files and directories that generic wordlists miss when an application names things predictably. It requires ffuf and a local Ollama daemon, and it is a discovery tool — not a vulnerability scanner. On BlackArch the two entry points are `brainstorm` (general path discovery) and `brainstorm-shortname` (a variant specialized in legacy Windows 8.3 short filenames).

## Typical use cases

### Run the basic discovery loop

Wrap your existing ffuf command.

```
brainstorm "ffuf -w ./fuzz.txt -u http://target.example.com/FUZZ"
```

Outcome: crawls target.example.com's links, then runs up to 50 cycles of LLM-suggested plus wordlist-driven fuzzing; discovered paths accumulate in all_links.txt under the default output directory /tmp/brainstorm.

### Tune model, cycles and hit criteria

Adapt the loop to a different Ollama model and stricter matching.

```
brainstorm "ffuf -w ./fuzz.txt -u http://target.example.com/FUZZ" --cycles 100 --model llama2:latest --status-codes 200,301,302,403
```

Outcome: runs 100 cycles with the named model, treating only the listed status codes as discoveries; `--prompt-file` swaps the suggestion prompt and `-o` redirects all link and ffuf output to a directory of your choosing.

### Hunt legacy 8.3 short filenames

Use the short-name specialist variant.

```
brainstorm-shortname "ffuf -w ./fuzz.txt -u http://target.example.com/FUZZ" "BENCHM~1.PY"
```

Outcome: the second argument seeds the short-name generator; confirmed 8.3-style filenames are written to all_filenames.txt in the output directory.

*No corpus evidence — derived from upstream documentation.*
