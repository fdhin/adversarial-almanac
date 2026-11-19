---
name: "mqtt-pwn"
categories:
  - blackarch-scanner
version: "43.40368e5"
homepage: "https://github.com/akamai-threat-research/mqtt-pwn"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "akamai-threat-research/mqtt-pwn"
github-stars: 456
github-pushed: "2024-08-09"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mqtt-pwn

> Interactive shell for MQTT broker pentesting: connect, discover topics, mine messages, brute-force logins and exploit known IoT devices.

## What it's for

MQTT-PWN (Akamai Threat Research) packages IoT broker assessment into a cmd2-based console. After connecting to a broker it samples traffic to build a topic list, stores every intercepted message in a local database for regex searching, brute-forces credentials with word lists, and runs device-specific exploits — extracting credentials from Sonoff smart switches and reconstructing GPS routes from OwnTracks publishes. A Shodan integration (API key set in its config) searches for exposed brokers. It is a console framework: work happens inside the `mqtt-pwn` shell through its subcommands, not through one-shot command lines.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Connect to a broker and discover its topics

Open the console, attach to the broker and sample what is being published.

```
mqtt-pwn
connect -o 198.51.100.10 -p 1883
discovery
topics
```

Outcome: the shell starts, connects (authenticating first when `-u user -w pass` are supplied), `discovery` subscribes and records traffic for its default timeout, and `topics` prints every topic seen during the scan; `topics -e` additionally exports the list to CSV.

### Search captured messages

Mine the recorded traffic for credentials and interesting payloads.

```
messages -tr password -l 50
```

Outcome: filters the messages captured by previous scans by topic regex (`-tr`, `-mr` for message-body regex), printing up to 50 matching records; `-e` exports the results.

### Brute-force broker credentials

Attack an authenticated broker with username and password lists.

```
bruteforce --host 198.51.100.10 -uf users.txt -pf passwords.txt
```

Outcome: runs the credential attack with the supplied files (built-in default lists apply when `-uf`/`-pf` are omitted, port defaults to 1883) and reports any working username/password pair for the broker.

### Exploit Sonoff devices and OwnTracks trackers

Pull secrets and movement history from known-vulnerable IoT traffic.

```
sonoff -p sonoff/ -t 30
owntracks
```

Outcome: `sonoff` listens on the given topic prefix for 30 seconds and extracts credentials and sensitive data from observed Sonoff traffic; `owntracks` plots routes from collected OwnTracks GPS coordinates.
