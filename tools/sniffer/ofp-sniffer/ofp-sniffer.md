---
name: "ofp-sniffer"
categories:
  - blackarch-sniffer
version: "1.0.0.r89.g0d907da"
homepage: "https://github.com/amlight/ofp_sniffer"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "amlight/ofp_sniffer"
github-stars: 15
github-pushed: "2026-07-06"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# ofp-sniffer

> Dissect OpenFlow control-channel traffic live on an interface or from a pcap, one readable block per message.

## What it's for

Debugging an SDN deployment means watching the OpenFlow conversation between controller and switch, which normally means Wireshark. OFP_Sniffer from the AmLight SDN team is a command-line alternative: it captures on an interface or reads a pcap file and dissects OpenFlow 1.0 messages — Hello, Echo, FlowMod, PacketIn/PacketOut, Barrier, vendor/NICIRA — into compact per-message text with type, XID and decoded fields, without any GUI. It can render flows in ovs-ofctl format, save output to a file, apply a JSON filters file (by message type, ethertype, switch DPID or port), map OpenFlow-proxy IPs to switch names via a topology file, and ship statistics to InfluxDB/Slack. It was built for the AmLight production network (FlowSpace Firewall, OESS, ONOS tested); the packaged 1.x line speaks OpenFlow 1.0 only. It observes traffic — it does not generate or modify flows.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Sniff the controller-switch channel live

Watch OpenFlow messages on the wire in real time.

```
sudo ofp-sniffer -i eth1 -f "port 6653"
```

Outcome: every OpenFlow message seen on eth1 port 6653 is printed with a timestamp, the endpoints, and dissected content (e.g. "Type: FlowMod(14) ... Command: Add(0) ... Action - OUTPUT Port: 2"), letting you follow the switch-controller handshake and flow changes as they happen.

### Dissect a saved capture

Work offline on recorded control traffic.

```
ofp-sniffer -r control.pcap
```

Outcome: the same per-message dissection read from `control.pcap` instead of a live device — useful for a post-mortem of a controller incident, with no interface or privileges required.

### Emit ovs-ofctl-style output and log it

Get flows in the format the switch CLI already uses.

```
sudo ofp-sniffer -i eth1 -f "port 6653" -o -w openflow.log
```

Outcome: messages are additionally rendered in ovs-ofctl format (`-o`) and a copy of the output is written to `openflow.log` (`-w`) while the console keeps printing — a transcript you can diff against `ovs-ofctl dump-flows` output.
