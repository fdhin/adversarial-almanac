---
name: "soot"
categories:
  - blackarch-binary
version: "3.4.0"
homepage: "http://www.sable.mcgill.ca/soot"
packaged-by: [blackarch, aur]
blackarch-builddate: "2021-02-01"
build-system: scripts
license: LGPL
meta-checked: "2026-11-09"
github-repo: "soot-oss/soot"
github-stars: 3104
github-pushed: "2026-11-06"
github-archived: false
github-issues: 347
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 450
gh-activity-checked: "2026-11-09"
---

# soot

> Java bytecode analysis and transformation framework built around the Jimple IR.

## What it's for

Soot is a long-running research framework (McGill's Sable group, now
soot-oss) for analyzing, optimizing, and transforming Java bytecode — and,
via its ecosystem, Android APK/DEX input. It converts class files into four
intermediate representations: Jimple (typed three-address code, its primary
IR), Shimple (SSA form of Jimple), Baf (streamlined bytecode), and Grimp
(aggregated Jimple suited to decompilation and inspection), then runs
user-defined analysis and transformation packs over them. It is primarily a
library for building program-analysis tooling (taint tracking, call-graph
construction, bytecode instrumentation) rather than an end-user CLI. Note the
project's own README: Soot has been succeeded by SootUp (released December
2022) for new projects, though classic Soot remains maintained because it
still leads on instrumentation capabilities and robust Android support.

## Typical use cases

### Convert a directory of classes to Jimple

```
soot --process-dir classes/
java -cp sootclasses-trunk-jar-with-dependencies.jar soot.Main --process-dir classes/
```

Outcome: every class under `classes/` is loaded, converted to Jimple, and
written as `.jimple` files under the `sootOutput/` directory — readable
three-address text you can inspect, edit, and even convert back to bytecode.
The first form uses the packaged `soot` wrapper; the second is the documented
upstream invocation.

### Analyze a Java 9+ project with modules

```
java -cp soot-trunk.jar soot.Main -pp -soot-modulepath modules/
```

Outcome: Soot resolves the named/automatic modules on the given module path
(including the JDK's own `jrt:/` virtual filesystem via `-pp`) and loads
their classes into its `ModuleScene` for analysis — the documented path for
module-era codebases.

### Embed Soot in an analysis project

```xml
<dependencies>
  <dependency>
    <groupId>org.soot-oss</groupId>
    <artifactId>soot</artifactId>
    <version>4.7.0</version>
  </dependency>
</dependencies>
```

Outcome: adds Soot to a Maven/Gradle project, giving programmatic access to
`Scene`, `SootClass`/`SootMethod`, the IR classes, and the pack manager —
the intended way to build custom static analyses and instrumentation passes
rather than shelling out to the CLI.

*No corpus evidence — derived from upstream documentation.*
