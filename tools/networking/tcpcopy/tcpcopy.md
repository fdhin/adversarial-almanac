---
name: "tcpcopy"
categories:
  - blackarch-networking
version: "1285.929511d"
homepage: "https://github.com/session-replay-tools/tcpcopy"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-28"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "session-replay-tools/tcpcopy"
github-stars: 4687
github-pushed: "2025-06-18"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcpcopy

> Replays live production TCP traffic against a test server to reproduce real-world load before deployment.

## What it's for

tcpcopy captures real requests on an online server and replays them against a separate test server, reproducing production workload diversity, network latency and resource usage while barely affecting the production host (extra CPU, memory and bandwidth only). It is a two-component system: `tcpcopy` runs on the online server, capturing live requests and sending raw-IP copies to the test server, while `intercept` runs on an assistant server, captures the test server's responses and feeds response headers back so tcpcopy can simulate the TCP interaction correctly. Typical uses are distributed stress testing, live pre-deployment testing, regression testing and performance comparison between versions.

## Typical use cases

### Capture test-server responses on the assistant server

Run the companion component where the test server's return traffic is routed.

```
intercept -i eth0 -F 'tcp and src port 3306' -d
```

Outcome: intercept listens on eth0 for response packets from the test server's port 3306 and forwards the response-header information to the online server's tcpcopy process — route the test server's return traffic to this assistant host to complete the loop.

### Replay live traffic to the test server

On the online server, forward a port's live traffic to the test environment.

```
tcpcopy -x 3306-198.51.100.20:3306 -s 198.51.100.30 -d
```

Outcome: live requests hitting port 3306 on the online host are copied to the test server at 198.51.100.20 (same port), which sees them as ordinary client traffic; `-s` names the assistant running intercept.

### Replay across subnets with explicit layer-2 hops

When the test target is not on the online server's LAN, spell out the MAC addresses.

```
tcpcopy -x 80@00:11:22:33:44:55-198.51.100.20:18080@00:66:77:88:99:AA -s 198.51.100.30 -o eth1 -i eth0
```

Outcome: captures on eth0 and sends on eth1, addressing the online machine's MAC (`@` before the dash) and the next hop toward the target (`@` after the target port) so the raw packets survive routing to a remote subnet.

*No corpus evidence — derived from upstream documentation.*
