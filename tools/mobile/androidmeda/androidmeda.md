---
name: "androidmeda"
categories:
  - blackarch-mobile
version: "v2.0.0.r1.ge2e5084"
homepage: "https://github.com/In3tinct/androidmeda"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-05"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "In3tinct/androidmeda"
github-stars: 381
github-pushed: "2026-01-29"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# androidmeda

> LLM-powered Android code analyzer: feeds decompiled app sources to OpenAI, Gemini or Claude APIs — or a local Ollama model — to rename ProGuard-obfuscated identifiers and produce a vulnerability report.

## What it's for

Androidmeda (Vaibhav Agrawal / In3tinct) applies large language models to decompiled Android code: after you decompile the APK with jadx or similar, it sends the app's Java sources to an LLM which proposes readable names for obfuscated classes, methods and variables, adds comments, and flags security issues it recognizes. Output is a `vuln_report` JSON summarizing potential vulnerabilities; with `-save_code true` it also writes the rewritten sources in package structure, tagging risky lines with `#SECURITY-ISSUE` comments. Providers are selectable — OpenAI, Google and Anthropic APIs (key in the `API_KEY` environment variable) or fully local inference via Ollama, which keeps the code on your machine. Two caveats from upstream: send only the app-specific source directories (whole decompiled trees including bundled libraries take extremely long), and treat LLM output as a lead generator for manual review — renaming and findings are probabilistic, not ground truth. The BlackArch package installs it as the `androidmeda` command with its Python dependencies in a bundled virtualenv.

### Scan decompiled code for vulnerabilities with a hosted model

Run the report-only analysis against one or more source directories.

```
export API_KEY='YOUR_API_KEY'
androidmeda --llm_provider google --llm_model gemini-1.5-flash -output_dir /tmp/ver/ -source_dir "sources/com/example/app/"
```

Outcome: writes `vuln_report` (JSON) describing potential vulnerabilities identified in the passed directories to `/tmp/ver/`; requires a valid API key for the chosen provider. Multiple directories go inside the one quoted `-source_dir`, space-separated.

### Deobfuscate and save the rewritten code

Have the model rename obfuscated identifiers and keep the result.

```
androidmeda --llm_provider openai --llm_model gpt-4.1 -output_dir /tmp/ver/ -source_dir "sources/com/example/app/" -save_code true
```

Outcome: in addition to the vulnerability report, writes deobfuscated `.java` files under the output directory in package structure, with readable names, explanatory comments and `#SECURITY-ISSUE` labels where the model saw security problems — ready for manual review.

### Run fully offline with Ollama

Keep the code and results on the local machine.

```
androidmeda --llm_provider ollama --llm_model llama3.2 -output_dir /tmp/ver/ -source_dir "sources/com/example/app/"
```

Outcome: performs the same analysis against a locally running Ollama model (ollama/ollama) with no API key or outbound traffic; size the model to your RAM — upstream quotes roughly 8-16 GB for 7B-class models and double that for 13B.

*No corpus evidence — derived from upstream documentation.*
