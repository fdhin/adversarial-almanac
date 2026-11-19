---
name: "pyjfuzz"
categories:
  - blackarch-fuzzer
version: "159.578d44c"
homepage: "https://github.com/mseclab/PyJFuzz"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-04"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "mseclab/PyJFuzz"
github-stars: 377
github-pushed: "2023-08-02"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pyjfuzz

> Python JSON fuzzer that mutates seed JSON objects with web-attack payloads, from the CLI, as a library, or via a built-in fuzzing web server.

## What it's for

PyJFuzz (mseclab, by Daniele Linguaglossa) fuzzes anything that consumes JSON: REST and mobile API endpoints, JSON parser libraries, browsers, and command-line executables. You give it a seed JSON object and it repeatedly applies mutation techniques — XSS, SQL injection, path traversal, command execution, header and template injection, random characters — selected by group (`CHPTRSX`) at a configurable aggression level (0-6); a strong-fuzz mode abandons the JSON structure entirely for parser-robustness testing. It ships as a Python library (`PJFFactory` and friends) and a CLI named `pjf` (the BlackArch package renames the binary to `pyjfuzz`), plus built-in HTTP/HTTPS servers that return a fresh mutation on every request for browser fuzzing, with a testcase server that records the payload whenever a monitored process crashes. It is payload-generation oriented, not coverage-guided. The upstream is Python 2 era and dormant.

## Typical use cases

### Fuzz a JSON object from the command line

Print an endless stream of mutated versions of a seed object.

```
pjf --J '{"user":"admin","role":"viewer"}' -t XS -l 6
```

Outcome: mutated JSON objects stream to stdout, each iteration applying one of the selected technique groups (X = XSS, S = SQL injection) to the seed's string fields at fuzz level 6; stop with Ctrl-C and pick payloads for replay, or redirect to a file to build a payload corpus.

### Fuzz a captured request body from a file

Replay-test every technique group against a saved JSON document.

```
pjf --F request.json -t CHPTRSX -i -ue
```

Outcome: the JSON in `request.json` is mutated with all technique groups (command execution, header injection, path traversal, template injection, random characters, SQL injection, XSS), pretty-printed (`-i`) and URL-encoded (`-ue`) so the outputs can be pasted directly into a replayed HTTP request body or a Burp Intruder payload list.

### Serve continuously fuzzed JSON over HTTP

Fuzz a client that fetches JSON, such as a browser or mobile app.

```
pjf --F response.json -ws --content-type application/json
```

Outcome: starts the built-in fuzzing web server on port 8080 (HTTPS on 8443) that returns a new mutation of `response.json` on every request; point the client under test at `http://127.0.0.1:8080` and let it consume mutated responses.

### Use as a Python library

Embed JSON mutation in your own harness.

```python
from argparse import Namespace
from pyjfuzz.lib import *

config = PJFConfiguration(Namespace(json={"test": ["1", 2, True]}, nologo=True, level=6, techniques="P"))
fuzzer = PJFFactory(config)
print fuzzer.fuzzed
```

Outcome: `PJFFactory` yields a newly mutated object each time `fuzzed` is accessed — here restricted to the P (path traversal) group — usable inside custom scripts or CI; `PJFServer`, `PJFProcessMonitor`, and `PJFTestcaseServer` add HTTP serving, crash monitoring, and automatic capture of the crashing payload. (PyJFuzz is Python 2, so `print` statements, not functions.)

*No corpus evidence — derived from upstream documentation.*
