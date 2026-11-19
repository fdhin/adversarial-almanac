# adversarial-almanac — the adversary's almanac

A two-part library for bug bounty hunters, CTF players, red teamers, and the AI agents that assist them:

- **`tech/`** — one folder per technology (146 of them): a human-facing overview
  (`<tech>.md`) plus a machine-executable bug-bounty methodology
  (`<tech>-bug-bounty.md`). Methodologies are phased field guides: triage
  tables ("where the money is"), command→expected-output blocks, decision
  points, evidence standards, and CTF-vs-bounty notes. Written to be executed
  by an authorized operator or an AI agent under written authorization.
- **`tools/`** — the tool side of the same library, organized by category:
  per-tool reference notes with catalog metadata, install recipes, and — for
  the bounty-core tools — full methodology supplements (workflow, recipes,
  noise/scope discipline, reporting evidence, verified references).

## Principles

1. **Techniques over CVE archaeology.** Old CVEs get one line of context;
   current advisories only where they define a live technique.
2. **Evidence-first.** Every methodology ends with what to capture and how
   to report it so triage can reproduce the finding.
3. **Authorization is part of the technique.** Single-request, low-volume
   discipline baked in; state-changing actions are gated explicitly.
4. **Provenance recorded.** Articles carry source and provenance metadata
   (see ATTRIBUTION.md for the upstream sources and their licenses).
5. **Placeholders by rule.** Targets in examples are example.com,
   *.example.com, or 198.51.100.0/24 (RFC 5737).

## Using with Obsidian

`tech/` and `tools/` are Obsidian vaults by design (wikilinks resolve within
each vault; the two are deliberately not cross-linked). Open either directory
as a vault. Markdown is plain — any editor works.

## License

CC BY-NC 4.0 (see LICENSE), constrained by upstream sources — see
ATTRIBUTION.md. Portions rewritten from sources under CC BY-NC 4.0
(HackTricks), GPL-3.0 (HowToHunt), and unlicensed public references
(pentest-book) with attribution preserved per article.

*Educational and professional-security use under explicit authorization.
Operators are responsible for staying within their engagement's scope.*
