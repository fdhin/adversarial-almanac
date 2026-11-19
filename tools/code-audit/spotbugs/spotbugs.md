---
name: "spotbugs"
categories:
  - blackarch-code-audit
version: "17817.eaabea0287"
homepage: "https://github.com/spotbugs/spotbugs"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-26"
build-system: java
license: LGPL
meta-checked: "2026-11-09"
github-repo: "spotbugs/spotbugs"
github-stars: 3965
github-pushed: "2026-11-06"
github-archived: false
github-issues: 480
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 440
gh-activity-checked: "2026-11-09"
---

# spotbugs

> Java bytecode static analyzer — the community successor to FindBugs — reporting bug patterns in compiled classes from a GUI or the command line.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SpotBugs dissects compiled Java bytecode — classes and jars, not source, so build the project first — against a large catalog of bug patterns covering correctness, bad practice, performance, multithreading and security-relevant mistakes. It is the spiritual successor of FindBugs, maintained by its community, and usable standalone (`spotbugs` wrapper script, or `java -jar $SPOTBUGS_HOME/lib/spotbugs.jar`) or through Maven, Gradle, Ant, Eclipse and SonarQube integrations. Analysis effort (`-effort:...`) trades precision against memory and time, priority thresholds filter the report, and dependencies the code calls into belong on `-auxclasspath` rather than in the analyzed set. It needs a JVM (JDK 11+ to run; it can still analyze bytecode compiled by older versions).

## Typical use cases

### Scan a jar from the terminal

Run the text UI over built artifacts.

```
spotbugs -textui -effort:max -high myapp.jar
```

Outcome: analyzes the jar's classes at maximum analysis effort (more precise detectors, more memory) and reports only high-priority bug instances; `-medium` (the default) widens to medium-priority findings and `-low` reports everything.

### Write report files

Produce artifacts for review or CI.

```
spotbugs -textui -xml=spotbugs.xml -html=spotbugs.html myapp.jar
```

Outcome: writes an XML report and an HTML report in the same run — `-xml:withMessages` embeds human-readable descriptions in the XML, and `-html:plain.xsl`/`-html:fancy-hist.xsl` select alternative built-in stylesheets. `-sarif=spotbugs.sarif` emits SARIF 2.1.0 for code-scanning tools instead.

### Limit analysis scope

Skip library code inside the artifact.

```
spotbugs -textui -onlyAnalyze com.example.app.- myapp.jar
```

Outcome: analyzes only the matching classes and packages — `.*` matches a package's classes, `.-` also includes subpackages, and entries prefixed with `!` are exclusions — which speeds up scans of fat jars. Trade-off noted upstream: some detectors lose accuracy when the whole application is not analyzed together.
