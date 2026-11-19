---
name: "h2spec"
categories:
  - blackarch-misc
version: "2.6.0"
homepage: "https://github.com/summerwind/h2spec"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-01-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "summerwind/h2spec"
github-stars: 737
github-pushed: "2023-10-15"
github-archived: false
github-issues: 34
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# h2spec

> RFC 7540 (HTTP/2) and RFC 7541 (HPACK) conformance testing for an HTTP/2 server endpoint.

## What it's for

h2spec throws protocol-level test cases at a running HTTP/2 implementation and reports which requirements of RFC 7540 and RFC 7541 the server satisfies or violates — malformed frames, sequence errors, header-decoding edge cases. Use it when you implement or deploy an HTTP/2 server, gateway, or proxy and need evidence of protocol correctness beyond "the browser works". It targets a single host, port, and path, and expects the server to answer `GET /` (or the given path) with a 200 and non-empty body; it measures conformance, not performance or load handling.

## Typical use cases

### Run the full suite against an HTTPS endpoint

Point h2spec at a TLS-enabled HTTP/2 server and run every test case.

```
h2spec -t -k -h target.example.com -p 443
```

Outcome: runs all HTTP/2, HPACK and generic test cases over TLS (skipping certificate verification), printing a pass/fail verdict per test and a final summary.

### Test one RFC section or single test case

Narrow a failing area to the exact requirement it violates using spec/section/test selectors.

```
h2spec http2/6.3/1 -t -h target.example.com -p 443
```

Outcome: runs only the first test case of section 6.3 (PING sequencing) of RFC 7540 instead of the whole suite.

### Include SHOULD-level requirements in strict mode

Upgrade the run from MUST-only violations to also flagging SHOULD-level deviations.

```
h2spec --strict -t -h target.example.com -p 443
```

Outcome: the same suite runs with strict test cases included, producing a more rigorous conformance report.

### Preview the test cases without running them

List which tests a run would execute, useful for scoping before pointing at a shared environment.

```
h2spec --dryrun -t -h target.example.com -p 443
```

Outcome: prints the titles of all test cases that would run, without sending any frames to the server.

### Produce a JUnit report for CI

Capture results in JUnit XML for a pipeline to consume.

```
h2spec -t -k -h target.example.com -p 443 -j report.xml
```

Outcome: writes pass/fail results to report.xml in JUnit format in addition to the console output, ready for CI ingestion.

*No corpus evidence — derived from upstream documentation.*
