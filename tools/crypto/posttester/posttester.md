---
name: "posttester"
categories:
  - blackarch-crypto
version: "0.1"
homepage: "http://packetstormsecurity.com/files/109010/MagicHash-Collision-Testing-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "Seabreg/PostTester"
github-stars: 0
github-pushed: "2019-05-24"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# posttester

> Java GUI that floods a chosen URL with POST requests carrying hash-collision parameters to demonstrate hash-flooding denial of service.

## What it's for

PostTester is the "MagicHash Collision Testing Tool" released around the 2011-2012 hash-collision disclosures: a small Swing application packaged as a jar (main class `magichash.PostTesterFrame`) that sends streams of crafted POST requests to a web application you specify in its window. Each request embeds a large set of parameter names that collide under the target platform's hash function — the jar bundles ready-made collision sets for Java (`java_free.txt`) and PHP (`php_free.txt`) — forcing the server's parameter parser into degenerate hash-bucket chains and CPU exhaustion. You launch it with `java -jar PostTester.jar` and drive it entirely through the GUI; it has no command-line interface and no maintained upstream. It is a demonstration artifact for testing hash-flooding defenses, so point it only at systems you are authorized to load-test.

*No corpus evidence — derived from upstream documentation.*

*GitHub mirror: `Seabreg/PostTester` — a faithful mirror; the committed `PostTester.jar` is byte-identical (sha512) to the PacketStorm jar BlackArch packages.*
