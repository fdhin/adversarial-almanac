---
name: "sentrypeer"
categories:
  - blackarch-defensive
version: "v3.0.0.r14.g02173c8"
homepage: "https://github.com/SentryPeer/SentryPeer"
packaged-by: [blackarch, kali]
blackarch-builddate: "2023-07-09"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "SentryPeer/SentryPeer"
github-stars: 212
github-pushed: "2026-11-06"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 187
gh-activity-checked: "2026-11-09"
---

# sentrypeer

> SIP honeypot that records attackers' source IPs and dialed numbers and can share them peer-to-peer.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SentryPeer (by Gavin Henry, sentrypeer.com) is a fraud-detection honeypot for VoIP: it listens for SIP probes (OPTIONS, REGISTER, INVITE and friends) from bad actors, saves the source IP address and the number they tried to call into a local SQLite database, and can push each observation as JSON to a webhook, log it to syslog (for fail2ban to block the IP), or replicate it to other SentryPeer nodes over a peer-to-peer DHT. The point is toll-fraud and voicemail-fraud prevention: when a phone on your network tries to call a number that probes have already collected, you can alert or block before the call completes. It is a self-contained collector — it does not inspect your live call traffic; you consume its data via the local RESTful API, the JSON log, or the DHT feed.

## Typical use cases

### Run the honeypot daemon with API and syslog

Stand up a node that answers probes and exposes what it collects.

```
sentrypeer -a -r -s
```

Outcome: binds UDP 5060 for SIP, replies to probes in responsive mode (`-r`), stores each bad actor in `./sentrypeer.db`, logs events to syslog (`-s`) so fail2ban can pick them up, and starts the RESTful API (`-a`) on port 8082. Add `-d` for debug output and `-p` to join the peer-to-peer DHT.

### Keep a JSON feed of collected bad actors

Get an event stream suitable for shipping into a SIEM or custom automation.

```
sentrypeer -j -l /var/log/sentrypeer/bad-actors.json
```

Outcome: appends one JSON object per observed SIP event (attacker IP, attempted number, method, timestamp) to the given file — the same payload that `-w https://example.com/hook` would POST to a webhook endpoint.

### Query collected data via the REST API

Consume the honeypot's findings from other tooling.

```
curl -s http://localhost:8082/ip-addresses | python3 -m json.tool
```

Outcome: returns the collected attacker IP addresses as JSON; `/numbers` lists collected phone numbers, `/numbers/<number>` shows who hit a specific number, and `/health-check` verifies the node is up.

### Terminate SIP-over-TLS probes

Catch attackers that only target sips/TLS listeners.

```
sentrypeer -t cert.pem -k key.pem -z 0.0.0.0:5061
```

Outcome: additionally listens for SIP over TLS on port 5061 (default `0.0.0.0:5061`, change with `-z`) using the given certificate and key, recording TLS probes the same way as UDP ones.
