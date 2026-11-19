---
name: "gadgetinspector"
categories:
  - blackarch-decompiler
version: "6.ac7832d"
homepage: "https://github.com/JackOfMostTrades/gadgetinspector"
packaged-by: [blackarch]
blackarch-builddate: "2020-03-25"
build-system: java
license: MIT
meta-checked: "2026-11-09"
github-repo: "JackOfMostTrades/gadgetinspector"
github-stars: 1094
github-pushed: "2021-06-15"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gadgetinspector

> Static analyzer that mines a Java classpath for usable deserialization gadget chains.

## What it's for

Gadget Inspector scans a web application archive or a set of JARs and
statically discovers gadget chains — sequences of method calls leading from a
deserialization entry point such as `readObject()` to a dangerous sink — that
could be assembled into a deserialization exploit. Penetration testers use it
to find exploitation primitives on a target's classpath; security engineers
use it to assess the impact of an unvalidated deserialization and prioritize
remediation. Findings are candidates, not proofs: the analysis ignores branch
satisfiability and only tracks a narrow sink list, so it reports false
positives and misses reflection-only chains. The analysis is memory-hungry —
allocate at least 2 GB of heap for small libraries and as much as possible for
real applications.

## Typical use cases

### Scan a library for gadget chains

Check a single dependency for call chains from a deserialization entry point to a dangerous sink.

```
java -Xmx2G -jar gadget-inspector-all.jar commons-collections-3.2.1.jar
```

Outcome: after several stages of classpath inspection (intermediate `.dat`
datasets are written and can be deleted), `gadget-chains.txt` lists each
discovered chain as an entry point followed by its method-call steps — e.g.
the classic `InvokerTransformer`-based chain in commons-collections.

### Analyze a whole web application archive

Treat a deployed application and all its bundled libraries as one classpath.

```
java -Xmx2G -jar gadget-inspector-all.jar target.war
```

Outcome: the WAR is exploded automatically, every bundled class and JAR is
included in the analysis, and `gadget-chains.txt` may contain chains spanning
several of the application's libraries.

*No corpus evidence — derived from upstream documentation.*
