---
name: "exabgp"
categories:
  - blackarch-networking
version: "nightly.r0.gf9bfb85"
homepage: "https://github.com/Exa-Networks/exabgp"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Exa-Networks/exabgp"
github-stars: 2315
github-pushed: "2026-11-02"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1732
gh-activity-checked: "2026-11-09"
---

# exabgp

> BGP speaker that turns routing into a script API: inject announcements, Flowspec rules and receive updates as text/JSON.

## What it's for

ExaBGP is a Python BGP speaker built for Software Defined Networking: it
peers with real routers and converts BGP messages into friendly plain text
or JSON that ordinary scripts can consume, while letting you drive
announcements from a declarative config file or an external program. It is
routinely used for anycast route injection driven by healthchecks, DDoS
mitigation via Flowspec discard rules, route servers and looking glasses,
and BGP labs. It is control-plane only — no forwarding, no FIB — and it
ships a built-in `healthcheck` backend for withdrawing routes when a
service dies.

## Typical use cases

### Announce a static route to a peer

Inject a prefix into a neighboring router with a config file.

```
cat > announce.conf <<'EOF'
neighbor 198.51.100.10 {
    router-id 198.51.100.20;
    local-address 198.51.100.20;
    local-as 64512;
    peer-as 64513;
    static {
        route 203.0.113.0/24 next-hop self;
    }
}
EOF
exabgp announce.conf
```

Outcome: establishes the BGP session to 198.51.100.10 and announces
203.0.113.0/24 via next-hop self; the prefix appears in the peer's
received routes, and stopping exabgp withdraws it.

### Block an attacking source with Flowspec

Push a discard rule instead of null-routing at every edge.

```
cat > flow.conf <<'EOF'
neighbor 198.51.100.10 {
    router-id 198.51.100.20;
    local-address 198.51.100.20;
    local-as 64512;
    peer-as 64513;
    family {
        ipv4 flow;
    }
    flow {
        route block-attacker {
            match {
                source 203.0.113.99/32;
            }
            then {
                discard;
            }
        }
    }
}
EOF
exabgp flow.conf
```

Outcome: sends the flow route to the peer, which starts discarding traffic
matching the source in hardware; `show flow route` on the router confirms
the installed rule.

### Hand received updates to a script

React to incoming routes with your own logic.

```
cat > watch.conf <<'EOF'
process log-routes {
    run /usr/local/bin/log-routes;
    encoder json;
}
neighbor 198.51.100.10 {
    router-id 198.51.100.20;
    local-address 198.51.100.20;
    local-as 64512;
    peer-as 64513;
    api {
        processes [ log-routes ];
    }
}
EOF
exabgp watch.conf
```

Outcome: every received BGP update is delivered to the script on stdin as
JSON, and anything the script prints back (such as `announce route ...`)
is executed on the session — the hook behind healthcheck-based route
injection and automated mitigation.

*No corpus evidence — derived from upstream documentation.*
