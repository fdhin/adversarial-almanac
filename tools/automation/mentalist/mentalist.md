---
name: "mentalist"
categories:
  - blackarch-automation
version: "v2.0.0.r1.g8223d8e"
homepage: "https://github.com/sc0tfree/mentalist"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-15"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "sc0tfree/mentalist"
github-stars: 2038
github-pushed: "2026-04-14"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# mentalist

> GUI wordlist builder that exports full lists or equivalent hashcat/John rules.

## What it's for

Mentalist (sc0tfree) generates wordlists graphically by chaining nodes: a Base Words node first, then any combination of Case, Substitution, Append and Prepend nodes whose attributes transform each word as it passes down the chain (with per-word de-duplication at every node). Instead of materializing a huge list, it can export Hashcat or John the Ripper rules that generate the identical keyspace at crack time, or just the base words for use with your own rules. Chains save as `.mentalist` files and reload on any machine. It needs tkinter; v2.0+ requires Python 3.11+. It is generation only — pair its output with hashcat or John.

## Typical use cases

### Build a wordlist interactively

Compose a chain and export the full list.

```
mentalist
```

Outcome: opens the GUI; add a Base Words source (typed list, file, or built-in lists), attach nodes such as Case > Capitalize or Append > Numbers: Small (0-100), watch the estimated word count and file size update in the top menu, then export the full wordlist to a file.

### Export rules instead of a giant wordlist

Let the cracker generate the candidates.

```
mentalist
```

Outcome: choosing the Hashcat/John rules output writes a rule file (plus your base words) that expands a small dictionary into the full chain keyspace at crack time — run e.g. `hashcat -m 0 hashes.txt basewords.txt -r mentalist.rule`. Note: First/Last substitution attributes are not expressible as rules and are coerced to All in this output, with a warning.

### Save and reload a chain

Persist the generation logic.

```
mentalist
```

Outcome: `Load/Save` > `Save Chain` writes a `.mentalist` chain file; `Load Chain` reopens it, drawing a red box around any user-specified input file missing on the current machine, with a `Fix...` button to re-point it at a local file.

*No corpus evidence — derived from upstream documentation.*
