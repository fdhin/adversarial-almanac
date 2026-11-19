---
name: "impulse"
categories:
  - blackarch-dos
version: "79.592a91b"
homepage: "https://github.com/LimerBoy/Impulse"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "LimerBoy/Impulse"
github-stars: 2876
github-pushed: "2025-04-10"
github-archived: false
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# impulse

> LimerBoy's multi-protocol DoS toolkit: SYN/UDP/ICMP/POD floods, NTP/memcached amplification, Slowloris and HTTP floods, plus SMS/email bombing.

## What it's for

Impulse is a Python denial-of-service toolkit that puts ten attack methods behind one CLI: transport-layer floods (SYN, UDP, ICMP, ping-of-death), reflection attacks (NTP and memcached amplification), application-layer attacks (HTTP flood and Slowloris), plus SMS/call and email floods. Every method shares the same four flags — `--method`, `--target`, `--time`, `--threads` — so each test is a one-liner, and the raw-packet methods must be run as root. It is meant for testing your own infrastructure's tolerance; the SMS and EMAIL methods target phones and mailboxes and have no legitimate load-testing use.

### HTTP flood against a web target

Application-layer pressure through the ordinary request path.

```
sudo python3 impulse.py --method HTTP --target http://target.example.com --time 60 --threads 50
```

Outcome: for 60 seconds, 50 threads issue requests against the URL while the tool prints its progress; on the target, watch request rate, worker pool saturation and error rate — that curve is the measurement.

### NTP amplification reflection

Layer-4 amplification with a spoofed victim address.

```
sudo python3 impulse.py --method NTP --target 198.51.100.10:123 --time 30 --threads 20
```

Outcome: for 30 seconds, crafted NTP requests are fired at public NTP servers with 198.51.100.10 as the spoofed source, so the amplified replies land on that address; the effect is read on the victim as an inbound UDP spike, not on the sending host. Requires root for raw sockets.

### Slowloris hold-open attack

Tie up the server's connection table with dribbling clients.

```
python3 impulse.py --method SLOWLORIS --target 198.51.100.10:80 --time 120 --threads 100
```

Outcome: 100 threads each open a connection to the web server and send partial headers at intervals to hold the sockets open; over the 120 seconds the server's connection table fills and new clients are refused or time out — no elevated privileges needed since it uses ordinary TCP sockets.

*No corpus evidence — derived from upstream documentation.*
