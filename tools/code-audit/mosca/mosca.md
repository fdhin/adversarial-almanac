---
name: "mosca"
categories:
  - blackarch-code-audit
version: "130.a7e725d"
homepage: "https://github.com/CoolerVoid/Mosca"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-01-15"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "CoolerVoid/Mosca"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# mosca

> Grep-like static bug finder in C driven by "egg" regex rule modules — sweeps many languages for dangerous patterns and saves results as XML.

*No corpus evidence — derived from upstream documentation (the upstream repository has since been removed from GitHub; the archived README and an archived rule module are the source).*

## What it's for

Mosca was a small C utility by CoolerVoid that searched source files for bug patterns the way grep does — explicitly "a simple grep", with no AST, control flow or taint analysis. Detection came from rule modules called *eggs*: plain-text files carrying metadata and a PCRE `::Match::#...#::` expression, for example a rule titled "Possible Command injection" matching `#system|popen|execve#`. Because matching is purely textual, any language works — the README advertised C, C++, Go, Java, PHP, JavaScript, C#, Swift and Ruby — and users could add their own modules in the `eggs/` directory. Results could be saved to XML, and the pitch was dropping it into CI/CD to collect "code smells" from external regex rules. The GitHub repository is gone (surviving copies exist on the Wayback Machine; last release v0.98, December 2022) and no CLI flags are documented in the surviving material beyond running the bare binary from a checkout containing the `eggs/` directory, so no verified command examples can be given. The packaged version lives on only in distro repositories such as BlackArch and the AUR. For the same regex-driven sweep job with maintained tooling, see graudit or semgrep.
