---
name: "turbo-intruder"
categories:
  - webapp
homepage: "https://github.com/portswigger/turbo-intruder"
origin: "pentest-book"
recipe: "upstream"
packaged-by: []
build-system: java
github-repo: "portswigger/turbo-intruder"
github-stars: 1822
github-match: "exact"
gh-checked: "2026-11-13"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
license: AGPL3
---

# turbo-intruder

> Burp Suite extension that blasts very large numbers of HTTP requests at a single host from Python-scripted attack definitions.

## What it's for

Turbo Intruder (PortSwigger) is a Burp Suite extension for sending huge numbers of HTTP requests and analyzing the results — it complements Burp Intruder on tasks needing exceptional speed, duration or complexity. Its hand-coded HTTP stack can seriously outpace asynchronous scripts, holds flat memory across multi-day runs, and copes with malformed requests that break standard libraries; an advanced diffing algorithm (adapted from Backslash Powered Scanner) filters boring responses so useful ones surface in a couple of clicks. Runs are defined as short Python scripts (`queueRequests`/`handleResponse`) rather than tab-and-click configuration — that is what makes multi-step sequences, signed requests and race conditions expressible, and also why upstream bills it as a tool for advanced users. Engine choices trade off: `Engine.THREADED` (custom HTTP/1.1 stack), `Engine.BURP2` (HTTP/2, single-packet attacks), `Engine.HTTP3` (HTTP/3), `Engine.BURP` (needs Burp's proxy/auth stack), `Engine.AUTO` picks the best available. It is designed for many requests against a single host — for one request across many hosts upstream recommends ZGrab instead, and it will not hold your hand: no personal support for usage trouble.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Fuzz a request at high speed

Send a request template once per payload, marked with a `%s` injection point.

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=5,
                           requestsPerConnection=100,
                           engine=Engine.THREADED)

    for word in open('/usr/share/dict/words'):
        engine.queue(target.req, word.rstrip())

def handleResponse(req, interesting):
    table.add(req)
```

Outcome: launch from Burp by sending a request to the extension (right-click → Extensions → Turbo Intruder), replacing the value to fuzz with `%s`; on Attack every payload is fired through the custom HTTP stack and each response lands in the results table, where the built-in diffing highlights the responses that differ from the crowd.

### Spray a curated wordlist from the extension itself

Skip external files and use the wordlists the UI provides.

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint)
    for word in wordlists.clipboard:
        engine.queue(target.req, word)

def handleResponse(req, interesting):
    table.add(req)
```

Outcome: `wordlists.clipboard` iterates the text on your clipboard (`wordlists.observedWords` and `wordlists.bruteforce` are the other built-in sources); omitting `engine=` selects `Engine.AUTO` in Burp Suite Professional and `Engine.THREADED` otherwise, so this is the minimal viable script.

### Race a request with the single-packet attack

Hit a transient window by releasing many requests simultaneously.

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2)

    # queue requests - they won't send yet
    for i in range(20):
        engine.queue(target.req, gate='race1')

    # send all queued requests simultaneously
    engine.openGate('race1')

def handleResponse(req, interesting):
    table.add(req)
```

Outcome: 20 copies of the request are held at a gate and then released in a single TCP packet over HTTP/2, so they arrive at the server together — ideal for limit-rate bypasses and other race conditions; in the results table a negative response time indicates the server answered before the request was fully sent, a sign of server-side processing overlap.

### Filter results in the response handler

Keep the table small by only recording what matters.

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint)
    for payload in ['p1', 'p2']:
        engine.queue(target.req, payload, label='probe')

def handleResponse(req, interesting):
    if req.status == 200 and 'error' not in req.response:
        table.add(req)
```

Outcome: `handleResponse` fires per response with `req.status`, `req.response`, `req.length`, `req.wordcount`, `req.time` (μs) and the injected `req.words` available, so only interesting hits reach the table — combined with the `label=` set at queue time you can triage multi-stage attacks quickly.
