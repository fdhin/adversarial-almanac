---
name: "javasnoop"
categories:
  - blackarch-reversing
version: "1.1"
homepage: "https://code.google.com/archive/p/javasnoop"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-11-30"
build-system: scripts
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "carterjones/javasnoop"
github-stars: 27
github-pushed: "2015-07-16"
github-archived: false
github-issues: 19
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# javasnoop

> GUI tool that attaches to running Java processes and hooks method calls to inspect, alter or replace their behavior.

## What it's for

JavaSnoop intercepts method calls in any Java desktop application or applet running on your machine — without source code, without decompile-recompile cycles: attach to the running JVM, pick the classes and methods, and hook them to view or change arguments and return values. It is built on the Java Instrumentation API plus bytecode engineering, and it fills the gap that network proxies leave: serialized, encrypted or custom-protocol client-server traffic can be manipulated at the method boundary instead of on the wire. It is a Java 1.6-era tool by Aspect Security: its launcher needs an old JDK (it copies `tools.jar` from `JAVA_HOME`/`JDK_HOME`), and while it runs it temporarily replaces `~/.java.policy` with a permissive policy, restoring the original on exit — so expect to install a legacy JDK before it will start on a modern host.

## Typical use cases

### Launch the UI against a legacy JDK

```
JDK_HOME=/usr/lib/jvm/java-6-jdk javasnoop
```

Outcome: the wrapper finds `tools.jar` in the given JDK, swaps in the permissive Java policy, and opens the JavaSnoop Swing UI, from which you attach to a running Java process and select the classes and methods to hook.

*No corpus evidence — derived from upstream documentation.*
