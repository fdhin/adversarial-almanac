---
name: "haka"
categories:
  - blackarch-networking
version: "0.2.2"
homepage: "https://github.com/haka-security/haka"
packaged-by: [blackarch]
blackarch-builddate: "2015-03-17"
build-system: cmake
license: Mozilla2
meta-checked: "2026-11-09"
github-repo: "haka-security/haka"
github-stars: 447
github-pushed: "2017-11-22"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# haka

> Framework that captures live or recorded traffic and applies Lua policy files that filter, modify, or alert on it.

## What it's for

Haka (haka-security) is a toolkit for writing security rules as Lua scripts instead of C. The `haka` daemon captures packets through netfilter_queue or libpcap and runs them through protocol dissectors plus user rule files that can drop, modify, or raise alerts on traffic; `hakactl` controls a running daemon, and `hakapcap` replays the same policies offline against a pcap capture. Because rules see parsed protocol content (IP, TCP, HTTP, SMTP, ...), a policy matches on decoded fields rather than raw bytes. Upstream development stopped around 2017, but the sample policies shipped under /usr/share/haka/sample/ (getting started, SQL injection detection, SMTP filtering) remain usable blueprints.

## Typical use cases

### Start the daemon with a policy configuration

```
sudo haka -c /etc/haka/haka.conf
```

Outcome: haka daemonizes (unless `--no-daemon`), attaches to the interfaces named in the config's `[packet]` section, loads the Lua rule file set by `configuration =` and starts filtering live traffic, with logs and alerts going to the modules chosen in the `[log]` and `[alert]` sections. `-r <rule.lua>` overrides just the rule file and `--opt <section>:<key>=<value>` any single parameter.

### Run a shipped sample policy

```
cd /usr/share/haka/sample/gettingstarted && sudo haka -c gettingstarted.conf
```

Outcome: runs the "gettingstarted" rule set on eth0 via nfqueue exactly as the upstream quickstart intends — the simplest template to copy when writing a first policy.

### Control and watch the running daemon

```
hakactl status
hakactl logs
hakactl stop
```

Outcome: `status` reports whether the daemon is up, `logs` streams its log output in real time, and `stop` shuts it down; `loglevel <level>` changes verbosity on the fly, and `console`/`interactive` open a remote Lua console and debugger on the daemon.

### Replay a policy against a capture offline

```
hakapcap /usr/share/haka/sample/sqli/sqli-simple.lua capture.pcap
```

Outcome: applies the SQL-injection detection policy to the packets in capture.pcap and prints each rule decision (alerts, drops) without touching live traffic; `-o out.pcap` writes the packets that survived the policy into a new capture file.

*No corpus evidence — derived from upstream documentation.*
