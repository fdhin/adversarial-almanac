---
name: "packet-o-matic"
categories:
  - blackarch-networking
version: "351"
homepage: "http://www.packet-o-matic.org/"
packaged-by: [blackarch]
blackarch-builddate: "2014-02-01"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "gmsoft-tuxicoman/packet-o-matic"
github-stars: 2
github-pushed: "2020-11-12"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# packet-o-matic

> Modular real-time packet processor: input modules feed rules with match/conntrack modules that dispatch packets to target modules.

## What it's for

packet-o-matic (by Guy Martin) is a modular network sniffer and packet processor. Packets enter through an input module (live pcap capture or a pcap file, DOCSIS), are matched against rules whose match modules describe the protocol header chain (ethernet, ipv4/ipv6, tcp/udp/icmp, vlan, rtp, ...), can be reassembled and tracked per connection by helper/conntrack modules, and are finally handed to target modules that act on them: dump payloads, write pcaps, reinject, open a tap interface, send TCP RSTs (tcpkill), or extract application content such as IRC chats, POP mail, TFTP transfers or RTP voice. It is configured interactively over a console rather than a CLI flag surface: start the daemon, telnet to port 4655, build the input/rule/target setup, and save it as XML. It ships no detection rules of its own — it is a traffic triage and extraction framework.

## Typical use cases

### Start the processor and its console

Launch the daemon, then connect to its management console.

```
packet-o-matic
```

Outcome: the daemon starts and listens on TCP port 4655; `telnet 127.0.0.1 4655` opens the `pom>` console where inputs, rules and targets are configured.

### Extract HTTP images from live traffic

Build a rule chain at the `pom>` console.

```
pom> input type set pcap
pom> rule add tcp.dport == 80
pom> rule enable 0
pom> target add 0 http
pom> target parameter set 0 0 dump_img yes
pom> target start 0 0
pom> input start
pom> config write
```

Outcome: the pcap input reads from the default interface, packets matching TCP port 80 flow to the `http` target, which writes images carried over HTTP to files; `config write` persists the entire setup to `pom.xml.conf` so the same processing starts on the next launch.

### Save matched packets to a pcap file

Capture what a rule matches, at the console.

```
pom> target add 0 pcap
pom> target start 0 0
```

Outcome: every packet matching rule 0 is appended to a pcap file by the `pcap` target, giving a filtered capture for later analysis in Wireshark or tcpdump.

*No corpus evidence — derived from upstream documentation.*
