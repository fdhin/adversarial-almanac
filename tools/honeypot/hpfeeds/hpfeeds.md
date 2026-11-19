---
name: "hpfeeds"
categories:
  - blackarch-honeypot
version: "414.34bf4d4"
homepage: "https://github.com/rep/hpfeeds"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: BSD
aliases: ["python2-hpfeeds"]
meta-checked: "2026-11-09"
github-repo: "rep/hpfeeds"
github-stars: 218
github-pushed: "2023-10-19"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hpfeeds

> Lightweight authenticated publish/subscribe data bus for honeypots: sensors publish to channels on a broker, analysts subscribe to the channels they need.

## What it's for

hpfeeds is the honeypot world's message bus (protocol originating with The Honeynet Project; the reference Python implementation here is by Mark Schloesser). Instead of point-to-point wiring between every honeypot and every analysis box, sensors publish events to named channels on a central broker, and consumers subscribe to the channels they care about; the broker authenticates each peer by ident/secret and controls which channels it may read or write. The wire format is simple and payloads are arbitrary — JSON is the convention — so clients exist in many languages, and tools such as conpot or SNARE/tanner-style setups can feed it directly. The package provides both the asyncio broker and a client library plus CLI; it is transport only, so what a channel contains is decided entirely by its publishers.

## Typical use cases

### Run the broker

Stand up the central hub that sensors and consumers connect to.

```
docker run -p 0.0.0.0:20000:20000 hpfeeds/hpfeeds-broker:latest
```

Outcome: the broker listens on TCP/20000 and keeps access keys (ident/secret pairs with their channel permissions) in a local SQLite database; authenticated sensors and subscribers can now connect, and the container's bundled client manages those keys.

### Tail a channel live

Subscribe to everything sensors publish on one channel.

```
hpfeeds subscribe --host 198.51.100.10 -p 20000 -i myident -s mysecret -c mychannel
```

Outcome: authenticates to the broker as myident and prints every message published to mychannel to stdout — a live feed of honeypot events for triage.

### Publish a single event

Inject one message onto a channel, for testing pipelines or scripting.

```
hpfeeds publish --host 198.51.100.10 -p 20000 -i myident -s mysecret -c mychannel '{"event": "ping"}'
```

Outcome: authenticates and publishes one JSON payload to mychannel; every current subscriber (including the `subscribe` tail above) receives it verbatim.

*No corpus evidence — derived from upstream documentation.*
