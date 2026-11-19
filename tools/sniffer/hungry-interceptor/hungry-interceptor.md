---
name: "hungry-interceptor"
categories:
  - blackarch-sniffer
version: "391.1aea7f3"
homepage: "https://github.com/nbuechler/hungry-interceptor"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nbuechler/hungry-interceptor"
github-stars: 6
github-pushed: "2017-05-25"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hungry-interceptor

> Flask data-ingestion gateway that stores pushed data in Mongo/Neo4j and forwards it to sibling services.

## What it's for

hungry-interceptor (nbuechler) is the receiving half of the author's "Logro" application stack: a Python 2 Flask web service that accepts data pushed to it by the front-end counterpart (`fixed-gateway`), formats it, stores it in MongoDB and/or Neo4j (py2neo), and forwards it to sibling engines such as the NLP scorer or ETL service — a router-like gatekeeper microservice. Despite being filed under sniffers, it performs no packet capture itself: it is an HTTP endpoint other components push into, not a network tap. The only documented interface is its development server, which takes a listen port as an argument; run it with MongoDB (`mongod`) and Neo4j already started if you want persistence. Its CORS configuration accepts everything by default and it was written as a 2016-era personal project, so expose it only on a trusted network segment.

## Typical use cases

### Start the interceptor service

```
hungry-interceptor 5000
```

Outcome: launches the Flask application (the packaged wrapper runs `app/runserver.py` from `/usr/share/hungry-interceptor`) listening on port 5000; data pushed by the gateway frontend is accepted over HTTP and routed to the configured Mongo/Neo4j stores and engines. Omitting the port makes it try port 80.

*No corpus evidence — derived from upstream documentation.*
